---
layout: post
title: "터미널을 예쁘게 꾸며보자"
date: 2017-03-08 20:20:42
image: '/assets/img/'
description: "터미널을 예쁘게 꾸며보자"
main-class:
color:
tags:
categories:
twitter_text:
introduction: "터미널을 예쁘게 꾸며보자"
---


터미널을 예쁘게 꾸며보자
------

어느 OS든 기본 터미널은 참 밋밋하다.
![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/terminal_1.png?raw=true)

이런 밋밋한 터미널에서 벗어나 예쁘고 편리하게 터미널을 커스텀 하려는 시도가 여럿 존재한다.

`oh my zsh` `powerline` `theme` 등등.....

이번엔 그 중 powerline스타일로 꾸미는 [powerline-shell](https://github.com/banga/powerline-shell)을 적용해보자

설치
---------
`powerline-shell`은 `zsh` `bash` `fish` 등의 쉘을 지원한다.

우선 프로젝트를 받고

~~~shell
$ git clone https://github.com/banga/powerline-shell
~~~

각자 입맞에 맞게 보고자 하는 정보들만 config.py에서 볼 수 있다.

~~~python
#config.py
SEGMENTS = [
# Set the terminal window title to user@host:dir
#    'set_term_title',

# Show current virtual environment (see http://www.virtualenv.org/)
    'virtual_env',

# Show the current user's username as in ordinary prompts
    'username',

# Show the machine's hostname. Mostly used when ssh-ing into other machines
#    'hostname',

# Show a padlock when ssh-ing from another machine
#    'ssh',

# Show the current directory. If the path is too long, the middle part is
# replaced with ellipsis ('...')
    'cwd',

# Show a padlock if the current user has no write access to the current
# directory
    'read_only',

# Show the current git branch and status
    'git',

# Show the current mercurial branch and status
#    'hg',

# Show the current svn branch and status
#    'svn',

# Show the current fossil branch and status
#    'fossil',

# Show number of running jobs
    'jobs',

# Show the last command's exit code if it was non-zero
#    'exit_code',

# Shows a '#' if the current user is root, '$' otherwise
# Also, changes color if the last command exited with a non-zero error code
    'root',
]

# Change the colors used to draw individual segments in your prompt
THEME = 'default'


~~~


환경설정이 다 되었다면 shell 설정파일을 만들면된다.

~~~shell
$ ./install.py
powerline-shell.py successfully genereated!
~~~

이제 각자 사용하는 쉘마다 아래 코드를 삽입하면 된다.

Bash 
----
~/.bashrc 파일

```shell
function _update_ps1() {
    PS1="$(~/powerline-shell.py $? 2> /dev/null)"
}

if [ "$TERM" != "linux" ]; then
    PROMPT_COMMAND="_update_ps1; $PROMPT_COMMAND"
fi
```

ZSH
----
~/.zshrc 파일 

```shell
function powerline_precmd() {
    PS1="$(~/powerline-shell.py $? --shell zsh 2> /dev/null)"
}

function install_powerline_precmd() {
  for s in "${precmd_functions[@]}"; do
    if [ "$s" = "powerline_precmd" ]; then
      return
    fi
  done
  precmd_functions+=(powerline_precmd)
}

if [ "$TERM" != "linux" ]; then
    install_powerline_precmd
fi
```

Fish
----
~/.config/fish/config.fish 파일 

```shell 
function fish_prompt
    ~/powerline-shell.py $status --shell bare ^/dev/null
end
```

쉘 설정파일에 추가 한 후 다시 적용하면 된다.

```shell
$ source ~/.bashrc
```


추가적으로 보여질 디렉토리 depth나 폴더이름 최대 길이 같은 설정을 할 수 있다. 

```  
  --cwd-mode {fancy,plain,dironly}
                        How to display the current directory
  --cwd-max-depth CWD_MAX_DEPTH
                        Maximum number of directories to show in path
  --cwd-max-dir-size CWD_MAX_DIR_SIZE
                        Maximum number of letters displayed for each directory
                        in the path
  --colorize-hostname   Colorize the hostname based on a hash of itself.
  --mode {patched,compatible,flat}
                        The characters used to make separators between
                        segments
```

본인은 아래와 같은 설정으로 사용하고 있는데 유용한 것 같다.

```shell
function _update_ps1() {
    PS1="$(~/python/powerline-shell/powerline-shell.py --mode flat --cwd-max-depth 3 $? 2> /dev/null)"
}

if [ "$TERM" != "linux" ]; then
    PROMPT_COMMAND="_update_ps1; $PROMPT_COMMAND"
fi

```


여기까지 적용이 완료되고나면, 아래와같이 예쁘게 바뀐 쉘을 볼 수 있다.

뿐만아니라 git을 사용중이라면 git의 상태, 브랜치명 등 여러 정보도 함께 볼 수 있다.

![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/terminal_2.png?raw=true)

