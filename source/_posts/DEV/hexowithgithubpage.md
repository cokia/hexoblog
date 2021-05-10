---
title: hexo 로 쉽게 나만의 블로그 만들기 with Github pages
date: 2019-01-23 21:20:25
categories: DEV
tags: dev
thumbnail: http://www.ybrikman.com/assets/img/blog/github-pages/github-pages.png

---


나는 평소에 글을 쓰는것을 그리 좋아하는 편이 아니다. 
그런데 요즘은 블로그에 글을 쓸 필요성을 느껴서 블로그 플랫폼을 찾다가,

누군가가 "개발자라면 github pages 써야지! " 라고 이야기를 해서 블로그를
hexo로 이전하게 되었다. (이전글 마이그레이션 해야되는데 팀에서 사용하는 confluence 글을 그대로 markdown으로 따올수 있어서 너무 좋다)

사실 개인적으로 hexo 랑 jekyll 를 고민을 많이했는데 jekyll는 테마도 안이쁘고, 나는 굳이 포스트 버전관리가 필요없어서 hexo로 결정하게 되었다. 

## hexo를 설치하는 방법


* Github 에서 "유저네임.github.io" 이름의 레포지토리를 만든다
* 내 컴퓨터에 클론뜬다. (git clone http://github.com/유저네임/유저네임.github.io)


	npm install hexo-cli -g
	npm install hexo-server -g 
	hexo init (폴더가 비어있지 않으면 에러가뜬다. 다른폴더에서 작업하고 깃 폴더로 이동하는걸 추천한다.)
 
 끝이다!
 (아 물론 푸시하고 커밋은 해야된다 )
 
 이제 블로그를 만들었으니 글을 써보자!
 나는 글을 못쓰는 편이라 바로올리기전에 원고형태로 쓰는것을 선호한다.
 	
    hexo new draft "타이틀"
    
    
**주의사항은 절대로 여기서 타이틀을 한글로 적으면 안된다! 블로그 글의 타이틀을 한글로 하고싶으면 생성된 후 마크다운에서 수정해야만 한다**


을 하면 새로운 draft가 생긴다.
/source/_draft/타이틀.md 경로에 생기는데,
이제 수정을 하면된다!

그리고, draft 를 post로 바꿀때는 
	
    hexo publish <Draft의 타이틀>
    
로 하면되는데 단점은 title을 정확하게 적어야한다는 것이다.

그래서 나는 source/_drafts 경로에 저장된 draft 파일을 source/_posts에 Drag & drop 이나 CP명령어로 옮기는 방법을 개인적으로 권장한다

이제 이 글을 github pages에서 보고싶다면 
	
    hexo deploy
를 하면되는데 그전에 _config.yml에
```
Deploy:
  type: git
  repo: [깃허브 레포 주소를 적으면된다.]
  branch: master
  ```
  
이렇게 수정을 해주면 된다.

수고많았다! 드디어 여러분도 여러분의 블로그를 github page로 만들었다. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExODQ1MDUwXX0=
-->