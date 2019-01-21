---
title: Elementary OS 5 Juno 설치 후
categories: [OS]
tags: [OS, linux, ubuntu, desktop]
---
# Elementary OS 5 Juno

[Elementary OS 공식 홈페이지(한국어)](https://elementary.io/ko/)

ubuntu 계열의 Desktop Linux 배포판으로 기본적으로 맥 OSX 비슷한 UI를 가짐. 테마를 더 손보면 더 맥 OSX 느낌을 낼 수 있음.

가볍다고 해서 가상머신에서 주로 사용하던 Linux Mint를 버리고 10년전 싱크패드 X220에 듀얼부트로 설치.

이하 설치 후 적당히 한 세팅

## zsh 설치

다음을 참고하여 zsh 설치

참고 [Oh My ZSH+ iTerm2로 터미널을 더 강력하게](https://medium.com/harrythegreat/oh-my-zsh-iterm2%EB%A1%9C-%ED%84%B0%EB%AF%B8%EB%84%90%EC%9D%84-%EB%8D%94-%EA%B0%95%EB%A0%A5%ED%95%98%EA%B2%8C-a105f2c01bec)

원문은 맥 기준이라 iTerm2 같은 건 없음

```sh
#zsh 설치
sudo apt-get install zsh
#oh my zsh 설치
#git 이 없으면 먼저 설치해야 함
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### 테마 변경

`~/.zshrc` 파일 수정

`ZSH_THEME="robyrussell"` 부분을 `ZSH_THEME="agnoster"`으로 수정

```sh
ZSH_THEME="agnoster"
```

### 폰트 변경

프롬프트 끝 부분이 깨지는 건 폰트 문제라고 하니 D2Coding 등으로 폰트 변경
dconf editor 등으로 수정(org > gnome > desktop > interface > font-name / monospace-font-name 항목)

### 프롬프트의 PC명 빼기

쉘 프롬프트의 사용자명@PC명 형식을 변경함

다음을 `~/.zshrc` 마지막에 추가

```sh
{% raw %}
prompt_context() {
    if [[ "$USER" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
        prompt_segment black default "%(!.%{%F{yellow}%}.)$USER"
    fi
}
{% endraw %}
```

### 프롬프트에 New Line 적용하기

프롬프트가 한줄이 아닌 두줄로 표시됨.

경로가 길어질 경우 편리함.

`~/.oh-my-zsh/themes/agnoster.zsh-theme` 수정.

`build_prompt() {...}` 블록의 `prompt_end` 앞에 `prompt_newline` 추가

```sh
build_prompt() {
  RETVAL=$?
  prompt_status
  prompt_virtualenv
  prompt_context
  prompt_dir
  prompt_git
  prompt_bzr
  prompt_hg
  prompt_newline //이부분을 추가 꼭 순서 지켜서
  prompt_end
}
```

위 수정 후 바로 아래에 다음 구문 추가

```sh
{% raw %}
prompt_newline() {
  if [[ -n $CURRENT_BG ]]; then
    echo -n "%{%k%F{$CURRENT_BG}%}$SEGMENT_SEPARATOR
    %{%k%F{blue}%}$SEGMENT_SEPARATOR"
  else
    echo -n "%{%k%}"
  fi

  echo -n "%{%f%}"
  CURRENT_BG=''
}
{% endraw %}
```

![두 옵션이 적용된 이미지](/images/2019-01-19-001.jpg)

### Syntax Hightlight 적용

없는 명령어가 빨간색, 처리할 수 있는 명령어면 녹색으로 표시됨

```sh
# 플러그인 설치
sudo apt-get install zsh-syntax-highlighting
# 플러그인을 적용합니다.
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```