---
title: ROOT CTF 2018 Wireup - FORMAT
date: 2019-01-25 00:36:14
tags: 
- HACKING
- CTF-WRITEUP
---


사실 이 문제는 대회를 끝나고 나서야 풀었다..

사실 99% 해놓고 머리가 안돌아가서 늦게 풀었다..



문제에서 format.png 라는 열리지도 않는 png를 준다

 

뭔가 시그니쳐나 헤더가 터진거같아서 png check를 돌려보니

![1](https://user-images.githubusercontent.com/24792377/51729849-b1c41600-20b8-11e9-9ff8-7170602b8d24.png)

IHDR이 터졌단다.. 그래서 다른 정상 PNG랑 같이 열어서 확인 해보았다.

![](https://user-images.githubusercontent.com/24792377/51729850-b1c41600-20b8-11e9-869b-e6949c9a7c72.jpg)

그랬다, 알고보니 0x00 이어야할 부분이 0x10인 것이다. 

처음에 나는 멍청했기에 0x10을 전부다 0x00으로 바꿨다
![](https://user-images.githubusercontent.com/24792377/51729851-b1c41600-20b8-11e9-9a96-9adcbfa40dd6.png)
그랬더니 이렇게 짤린게 나왔다. 대회가 끝나기 직전 6시간동안 내가 삽질한 결과다.

대회가 끝나고 계속 생각하다보니 "0x10은 0x00으로 바꿔줘야되는거아닌가?" 라는 생각이 들었다.

그래서 위 파이썬 코드를 짜서 돌리니 플래그가 나왔다.

{% gist aa5c4e91a356e8fd0b418589378e4f36 %}

open("fix.png","w+b").write(t)
![](https://user-images.githubusercontent.com/24792377/51729852-b1c41600-20b8-11e9-852c-68bbf4c7e05f.png)

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0MDY4MDU4MzhdfQ==
-->