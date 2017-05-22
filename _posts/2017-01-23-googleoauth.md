---
layout: post
title: "GoogleOAuth를 구현해보자"
date: 2017-01-23 22:43:58
image: '/assets/img/'
description: 'googleOAuth'
main-class: 'python'
color: '#1D91F5'
tags:
- googleOAuth
- python
categories:
twitter_text:
introduction: 'python을 통해 googleOAuth를 구현해보았다'
---

google oAuth
=====
<br>

왜?
---

caly 서비스는 유저의 기존사용 캘린더에 대한 관리허락을 필요로 한다.
구글 캘린더 유저의 캘린더 관련 api를 호출하기 위해선 유저의 인증 및 권한확인이 필요하다.

<br>

oAuth의 대략적인 flow
---

oAuth의 인증 플로우는 아래와 같다.

1. 유저가 google 로그인페이지에 접속한다.
2. caly서버에서 원하는 권한 scope가 설정되어있으며 해당 권한을 요청하는 구글 uri를 반환해준다. 
3. 유저는 해당 반환되어진 구글 인증 페이지에서 해당 권한울 확인하고 동의하에 허락을 클릭하게 된다.
4. 유저의 클릭 순간 구글서버에서 caly 콜백 메소드로 요청이 날라온다.(유저 credentials.)
5. 해당 credentials로 유저를 caly서버에 가입시키고, 해당 userToken을 통해 허락된 서비스의 정보를 접근할 수 있게 된다.

똑똑하다..


<br>

1.credentials을 만들자.
---

구글 oAuth를 사용하기위해 사용자 인증 정보를 만들어아햔다.

[구글콘솔](https://console.developers.google.com/apis/credentials?project=swmaestro-156107)에 접속하고

~~~
사용자 인증정보 탭 => 상요자인증정만들기 => oAuth클라이언트 ID
~~~


클라이언트 ID를 해당 플랫폼에 맞게 선택하여 만든다.

필자는 웹페이지를 선택했다. 

해당 콘솔에서 확인할 이름을 설정해주고,

승인된 자바스크립트 원본에 oauth인증을 사용할 서버의 도메인 주소를 밉력해준다. 

포트까지적어준다.

승인된 리다이렉션 URI에는 oAuth인증을 한 후 인증된 해당 유저의 정보(userToken..)를 콜백 받을 수 있는 uri를 말한다.

다 설저을 맞추면 해당 클라이언트ID에 대한 정보를 `client_secrets.json`파일로 받을 수 있게된다. 

해당 파일을 받아둔다. 

<br>

2.웹서버를 만들자.
---

이제 oAuth를 요청하고  정보를 받는 웹서버를 하나 만들어보자.

caly의 서비스는 python으로 되어있다.

google api를 사용하기위해 apipython 라이브러리를 설치한다.

[*참고 googleClientPython ](https://developers.google.com/api-client-library/python/start/installation)

~~~
	pip install --upgrade google-api-python-client
~~~


이제 1번에서 언급했던 flow를 잘 생각하고 코드를 작성한다.

(구글의 [oauth2.0 webserver Applications](https://developers.google.com/api-client-library/python/auth/web-app)를 참고하였다.)


구글의 예를 그대로 가져다 쓰면되는데 discovery를 찾지 못하는 경우가있다.

~~~
	cannot find module discovery
~~~

이와 유서한 에러인데(명확하진 않다)

~~~
	from googleapiclient import discovery
~~~

필자는 검색후에 이렇게 해결하였다.

이유는 기존 코드에서 해당 라이브러리의 위치를 명확히 찾지 못한것으로 생각된다.(필자가 잘못설치했거나..)


상세코드는 아래와같고 주석으로 설명을 달아 두었다.

~~~
import json

import flask
import httplib2

from googleapiclient import discovery
from oauth2client import client
import uuid

app = flask.Flask(__name__)


@app.route('/')
def index():
//session에 creentials가 없다면 유저가 최초 로그인한 경우이다.
//oauth2callback으로 리다이렉팅해준다.(oauth2callback의 로그인 분기를 통해 인증 요청을 보낸다.)
  if 'credentials' not in flask.session:
    return flask.redirect(flask.url_for('oauth2callback'))
  credentials = client.OAuth2Credentials.from_json(flask.session['credentials'])
  if credentials.access_token_expired:
    return flask.redirect(flask.url_for('oauth2callback'))
//session에 credentials가 존재할 경우이다. => 이미 oauth로 유저가 가입된 상태이다. 이미 가입된경우이고 drive 읽기를 사용해도 된다고 허락받았음으로 해당 api를 사용 할 수 있게된다.
유저 드라이브 파일리스트가 화면에 뿌려질 것이다
  else:
    http_auth = credentials.authorize(httplib2.Http())
    drive_service = discovery.build('drive', 'v2', http_auth)
    files = drive_service.files().list().execute()
    return json.dumps(files)

//유저가 승인하고난뒤의 콜백 그리고 최초 로그인창을 띄우게하는 코드이다.
@app.route('/oauth2callback')
def oauth2callback():
//위 1번과정에서 받은 oauth clientID 정보이다. 이정보가 구글에게 우리서버가 인증된 caly서버라는것을 알려줄 것이다.
// scope는 유저에게 어떤 서비스에 있어서 승인을 받을것인가에 대한 이야기다. + 로 여러 승인을 받을 수 있다.
  flow = client.flow_from_clientsecrets(
      'client_secrets.json',
      scope='https://www.googleapis.com/auth/drive.metadata.readonly',
      redirect_uri=flask.url_for('oauth2callback', _external=True),
      include_granted_scopes=True)
  //요청에 code값이 없을 경우인데, 바로 최초 로그인 상황이 된다. 
  //이상황에서는 로그인 및 승인 uri를 전달한다.
  //유저는 googleOauth화면을 맞이하게 될것이다.
  if 'code' not in flask.request.args:
    auth_uri = flow.step1_get_authorize_url()
    return flask.redirect(auth_uri)
  //요청에 code값이 있을경우인데. 이경우는 유저가 아이디 패스워드를 인증하고 승낙까지 마무리할 경우 발생하는 분기이다.
  //code값을 이용해 credential(유저정보)를 받아오고 이를 flaksSession에 저장한다. 
  else:
    auth_code = flask.request.args.get('code')
    credentials = flow.step2_exchange(auth_code)
    flask.session['credentials'] = credentials.to_json()
    return flask.redirect(flask.url_for('index'))


if __name__ == '__main__':
//flask에서 세션을 사용하기위해선 아래와같이 secretKey를 설정해주어야한다. 
  app.secret_key = str(uuid.uuid4())
  app.debug = False
  app.run()
~~~

위 코드는 다소 복잡해보인다.

그러나 oAuth 플로우를 잘 생각해보고 따라가보면 플로우의 모든 내용이 코드에 들어 있음을 확인할 수 있다. 


마치며
---
oAuth는 굉장히 똑똑하고 부드러운 로그인 및 권한 인증 로직이다. 

유저는 새로운 서비스에 대해 가입해야하는 귀찮음을 덜어주고,

개발자 입장에서 손쉽게 유저의 가입을 유도할 수 있으며, 우리 서비스에서 필요한 외부 서비스 권한들을 쉽게 받아와 사용할 수있다.

다만 악의적으로 무분별하게 모든 권한들 혹은 가입하는 서비스에 필요치도 않는 권한을 요구하는 경우가 있을 수 있음으로 유저들은 항상 권한 사항에 관해 주의깊게 살펴봐야 할것이다.



