---
layout: post
title: "Classification"
date: 2017-04-24 05:17:01
image: '/assets/img/'
description: '머신러닝'
main-class: 'machine learning'
color: '#1D91F5'
tags:
- ML
categories:
twitter_text:
introduction: 'binary classification'
---

[Supervised 머신러닝](https://calyfactory.github.io/supervised-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D/)에서 살펴봤던 Linear regressioin 이후,

오늘은 classification 중에서도 binary classification을 알아보겠다. (본 포스팅 모든 자료의 [출처](http://hunkim.github.io/ml/))

Binary(Logistic) classification
===
Linear regression은 수치를 예측해내는 그래프였다면,

Classification은 분류값을 예측하는 그래프다.

특히 Binary(Logistic) classification은 0, 1 encoding이라 해서 Pass/Fail을 판별하기도 한다.

E-mail 스팸필터나 Facebook 각각의 feed를 보여줄지 숨길지 등이 이에 해당한다.

![post14_binaryclassification.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_binaryclassification.png?raw=true)

위 그림은 공부시간에 따라 Pass / Fail이 정해진다는 전제하에 그린 그래프다.

이 그래프를 보면 언뜻, Linear regression으로 해결할 수도 있겠다는 생각이 든다.

다음 그래프와 같이 말이다.

![post14_linear1.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_linear1.png?raw=true)

하지만 이 그래프에는 큰 결함이 두 가지 존재하는데, 다음 그림에서 보겠다.

![post14_linear2.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_linear2.png?raw=true)

첫번째 결함으로, 기존에 PASS였던 것이 FAIL로 평가되는 기준 변경이 문제이다.

두번째 결함은 그래프 전체가 망가진 다는 점이다.

예를 들어 x의 값이 위와같이 100일 때 weight는 50을 가지게 되는데,

기존의 값들과 격차도 심하기 때문에 그래프 자체가 건강하지 않다(좋지않다)고 표현하게 된다.

그래서 어떤 값을 넣더라도 weight가 0~1을 가지는 모델을 찾게 되었는데,

그 모델은 다음과 같다.

![post14_gx.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_gx.png?raw=true)

![post14_hypothesis.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_hypothesis.png?raw=true)

이 *Logistic hypothesis*는 다른 그래프를 가진 만큼, cost function에서도 차이가 난다.

![post14_comparecost.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_comparecost.png?raw=true)

이는 기존의 minimize cost function으로 logistic classification의 hypothesis를 나타낸 것이다.

그림과 같은 그래프를 가지게 되므로, 새로운 hypothesis는 local minimum이 bad prediction을 나타낼 수도 있다.

새롭게 고안된 Logistic classification의 cost함수는 다음과 같다.

![post14_logisticcost.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_logisticcost.png?raw=true)

y=1일 때와 y=0일 때 그래프를 붙이면 이전의 cost함수와 같은 global minimum을 보장하는 그래프가 되는 것을 확인할 수 있다.

minimize cost에서의 gredient descent는 다음과 같은 식으로 구할 수 있으며, 범위를 벗어나는 내용이므로 설명은 생략한다.

![post14_logisticgredieent.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post14_logisticgredieent.png?raw=true)
