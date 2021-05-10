---
title: ROOT CTF 2018 Wireup - HUBGIT
date: 2019-01-25 16:16:19
tags: 
- HACKING
- CTF-WRITEUP
---

hubgit 이라는 이름에서 추측할 수 있듯이 문제파일을 받아 압축을 풀면 .git 폴더가 나오게된다.

![1](https://user-images.githubusercontent.com/24792377/51731016-ecc84880-20bc-11e9-9093-1f8e933bd49e.png)

위와 같은 형태의 파일 구조를 가지고있는데 의심스러운 파일이 몇개 있다.

먼저 로그부터 확인해보자.

![2](https://user-images.githubusercontent.com/24792377/51731018-ed60df00-20bc-11e9-8642-75400dd8a281.png)

리드미를 커밋했지만, 실제로 커밋된 내용은 하나도없다.
그럼 아까 파일중에 의심스러웠던, COMMIT_EDITMSG 를 보자.

{% gist 2ba28ad88fd236037419ac27195bc590 %}

먼저 EDITMSG 파일을 보면 커밋 메시지가 수정되었다는걸 알 수 있고 아마 저 수정 전 메시지가 플래그일 것이다.

그런데 하위폴더까지 다 보다보니 object가 readme 하나가 커밋된것에 비해 비정상적으로 많다..?

![3](https://user-images.githubusercontent.com/24792377/51731019-ed60df00-20bc-11e9-824c-2b3f18765281.png)

그리고 각각 폴더에는 일반 텍스트 뷰어로는 안열리는 파일이 있다.

검색을 해보니 zlib 으로 decompress 하면 된다고 한다.

그래서 [마침 파이썬을 공부하던 참이라는 핑계로]  파이썬 코드를 짜서 풀어보았다.

{% gist 88152ea30555f056b7acbc6103a3b8d1 %}

코드를 살짝 요약하자면 glob 라는 라이브러리를 통해 object/*/*  폴더 (ex: /.git/object/0a/db1c2987b8b6330b1e20f6b02b310f54dfc1c4) 의 모든 요소를 탐색해 전부다 zlib decompress 를 해서 출력해준다.



그렇게 하면

![4](https://user-images.githubusercontent.com/24792377/51731020-ed60df00-20bc-11e9-81c8-84a5b7a1782c.png)

플래그가 나오게 된다.

**FLAG{GIT_8rob1em_7h@t_C4n_b3_50lv3d_in_O63_M1nu7e!}**