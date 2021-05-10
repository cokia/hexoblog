
---
title: Ansible 알아보기
date: 2019-12-01 23:26:29
tags: Ansible

---

# Ansible 알아보기

![](https://cdn-images-1.medium.com/max/1800/1*hdwjXl1x4Q3VXmL7UG1XrQ.png)

## 서론

  

평소에 Ansible 은 꽤나 자주 듣어봤지만 한번도 접해보거나 찾아보진 않았다 (이놈의 귀차니즘은 ...)

  

사실 서버를 클러스터링 해서 쓸 필요성도 못 느끼고있었고, 서버를 그렇게 많이 관리하는것이 아니다 보니 그럴만할지도 모르겠다.

  

마침 오늘 vue로 포트폴리오를 만들다가 갑자기 너무 하기싫어서 뭘하지 하다보니 Ansible이 떠올랐고, 야매로(?) 공부한 부분들을 간단하게 정리해보고자 한다.

  

그리고 필자는 Ansible을 엔시블이라고 발음해야될지 엔서블이라고 발음해야될지 모르겠기에 그냥 Ansible 이라고 쓰겠다. (사실 이미 귀찮아서 복사 해놨다.)

  

## Ansible 이란?

  

"여러대의 서버를 쉽고 빠르게 관리하는 도구" 로 많은 사람들이 정의하고있다.

  

좀 고오급진 말로 표현하면 ****"시스템 환경 설정 및 어플리케이션 배포 자동화 플랫폼" 정도로 이야기할 수 있는데,****

  

python 으로 개발하고 YAML 로 설정하고 json API로 슥삭슥삭 할 수 있어서 배우는것도 굉장히 쉽게 배울수있다고 한다. 멱등성 이라는것을 보장하는데, 쉽게 말하면 "한번 설치한 건 다시 설치하지 않는다." 정도로 이야기 할 수 있다.

  

[http://docs.ansible.com/](http://docs.ansible.com/) 에서 공식 문서를 볼 수 있다.

  

## 비교

  

난 당연히 클러스터링 플랫폼에 아는게 없기때문에 Ansible이 유일무이한것 인줄 알았는데 꽤나 비슷한게 많이 있었다.

![Infrastructure as Code: Chef, Ansible, Puppet, or Terraform? | IBM](https://1.cms.s81c.com/sites/default/files/2018-11-19/Screen%20Shot%202018-11-19%20at%205.03.18%20PM.png)

IBM발 자료인데, 대표적인 Infrastructure as Code 툴들을 비교한 사진이다. 이때까지 테라폼과 Ansible을 제외하고는 주류 툴은 아니라고 생각했는데 puppet 이나 chef도 생각보다 많이 쓰고 있어서 놀랐다. 
  

## 특징


### 가장 큰 특징으로는 agent가 없어도 된다는 것이다!!

Ansible 을 실행할 서버 이외의 관리당할 서버에는 agent를 따로 설치할 필요가 없다. ssh를 통해서 agent의 역할을 대체한다.

구조가 잘 설명된 그림이 있어서 가져왔다.

  

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/6b9af262-9ad2-4484-a0f8-1086b9299c9e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45A4CUGDOH%2F20191201%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20191201T142238Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjELT%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJHMEUCIHOHDPpgiCQSB0OXU16ySLi0hpB%2FLI9nSYsXRNNCQ%2F2yAiEA12d6I3AiJxjPMUiBUa%2FNIkigoKI4h5fNj6XhM7ieFSoq2wII7f%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAAGgwyNzQ1NjcxNDkzNzAiDKf46M981T%2BGvFzmjSqvAlajcZ3eCLfmXZOIHWeoaksy70kOztknR%2B1uYh8Gs7H5GrOF9fXjn25IZJ9K4I3q2C148aJFgCk6q8Ct%2B7QH%2FkanHThr42IeAziEhlYtPGjWhX1IDyQH8elAGN8v1Bua8I735SqPYdfpenBsoxxfbp2i8hVQK%2BCVSXoFyBTDmdQHxriccqVu%2F9DeK%2FoYBMYJ1%2Bgj0YXaCSfBuKtH6WGWkPZN801Xy8n5ZHgawHWWwzDsV%2BG0G34taYA0BDBlANvZgdJ2V5JVSkXvZIZH1KWBR5Z2pCzyp8nUk9SQ86lbqJqNWrFkSQZ3B3MIsuu8wKZm6Y7g5SxOzjzZ5joR1gwToyHcr2mVBTwf%2BgdbsZaqsgUlF6iJnPMeFrtaH3q%2FoBaFZLEgoKxZ1Xj1LnNMyMCY%2BDDE0o7vBTrNAhhHYn5GcLu0ZYFdJbtp88P78cpli0Ay3NA35Amy9oYtUoJYxJg78LoM4xgSZeXL8QcNFN7eykJFjb%2BQGet0Hb6aUMZGm6I55Ft646HwxDLRb4A2kYnFwEDGI3%2F3%2BcdSN6L38wPyW48VCDG24XYFmjEbN2NfaTah7fI5mQxGJH7pVURvQYF9lg0xus%2BPoGNYDP0BqeDwYHHa5mZXg%2BYuO7i0wicgvkxreStcxYhNv6t%2BvKBhviwd%2BOMEAF9yxRtefQ5E1sGWSUrinT5FYNWuOZ9GMoKvaju9078qE9O7xXLI6WY4LnMNK3fFqKzQOeywI%2BOutB5xEWUNDNXF3hXUimNPUThG7QQBf1f1VOkhcydKBexaY2m9YBJ2fUUod25dmKFUVN%2FWLe0deNGBf%2FOpC%2BINWSd8DCOrI9XaNtvQPdSftP%2BmIVJky2z%2Bzh6jfQ%3D%3D&X-Amz-Signature=39cb30d55cde8b165e587bd38a0c420c0e052fddd5dfdb93407ba84f2edaec68&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

  

유저가 ****Ansible****콘솔에서 명령을 날리면, 클라우드 환경이든 온프레미스 환경이든 네트워크 장비든 한꺼번에 관리할 수 있는것이다. 내부/외부망에 관계없이 말이다.

  

## 구성

  

- 인벤토리(inventory) : ‘어디에서’ 앤서블을 실행하는가

- 모듈(module) : ‘무엇을’ 앤서블에서 실행하는가

- 플레이북(playbook) : ‘어떻게’ 앤서블을 실행하는가

  

이렇게 Ansible Stack 이 이루어 진다.

  

인벤토리는 서버의 접속 정보를 확인할 수 있게 해주고,

  

모듈은 Ansible을 통해 실행할 수 있는 각각의 명령 (쉽게말하면 플러그인),

  

플레이북은 Shell Script와 같은 스크립트로 작성된 모듈의 집합이다.

  

오늘은 졸려서 이정도로 간단히 정리만 하고자한다. 내일 일어나서 ****Ansible**** 설치해서 가지고 놀며 후기를 남기도록 하겠다 :)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg1NTE1MjU0NF19
-->