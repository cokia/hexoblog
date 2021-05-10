---
title: DIMICTF 2019 예선 라이트업
date: 2019-07-09 10:03:39
tags: -HACKING
---

# KDMHS CTF 2019 (전국 청소년 모의해킹 대회 - 예선)
![image](https://user-images.githubusercontent.com/24792377/60890587-87e16300-a296-11e9-87fc-e5337c537eac.png)

`9등 슥--삭` 어쩌다 보니 Mic Check을 2등으로 풀어서 다른문제를 더 안풀어도 본선갈 각이 잡혔지만 그래두 문제를 몇개 풀어 보았다. 더 꿀잼인건 종료 44초를 남겨두고 문제를 하나 더 풀어서 슥삭 해서 등수가 올라가 버렸다는거,,

## Web 
### 5shared
![image](https://user-images.githubusercontent.com/24792377/60890982-603eca80-a297-11e9-9e11-fbed1ec44242.png)
먼저 들어가면 요로코롬 화면이 나온다. jpg png 와 같은 이미지 파일만 업로드가 가능하다고 한다.
문제에서 서버쪽 파일을 제공 해줬다. 파일 업로드 폴더이름이 ~uploads 길래 들어가 보았다.
![image](https://user-images.githubusercontent.com/24792377/60891002-6765d880-a297-11e9-86e7-128fc6cafc31.png)
오 뭔가 남들이 페이로드를 올리면 여기 올라올 삘이였다. 그럼 존버타고 플래그를 꺼억 하면 되는건가 싶어서 다른문제 풀러 총총 도망갔었다
![image](https://user-images.githubusercontent.com/24792377/60891118-a09e4880-a297-11e9-8758-1d0915589ef0.png)
역시 예상대로 페이로드가 미친듯이 올라왔고, 그중에 하나를 건져서 플래그를 얻었다 (너무 날로먹은 기분이다)
![image](https://user-images.githubusercontent.com/24792377/60891131-a72cc000-a297-11e9-9b8f-f0b3dd5a4a9d.png)
Flag is `DIMI{expl0d3_pht_g3t_sh3lL_:P}` 

![image](https://user-images.githubusercontent.com/24792377/60891388-2fab6080-a298-11e9-984f-d63d01dae45d.png)



## MISC
### MIC CHECK

![image](https://user-images.githubusercontent.com/24792377/60891449-4d78c580-a298-11e9-8851-2ce07746a794.png)

맠쳌은 2등으로 슥삭 해버렸자너~ 
Flag is `DIMI{A-A-A-A---Mic-Check!}`

### Dimi Math
사실 이거 푸는데 시간을 거의 다 소모했다 (10시 ~ 3시)
사실 문제 자체를 푸는게 오래걸린게 아니라 중근 찾느라 개고생했다

일단 접속하면

![image](https://user-images.githubusercontent.com/24792377/60891644-aa747b80-a298-11e9-9403-8602190271b9.png)

요런 이미지가 나온다
사실 나도 pwntools를 잘쓰는게 아니라서, No. 1) 앞줄까지 잘라서 받으려고 recvuntil() 함수를 썼는데 그뒤로 recvline() 함수가 이상하게 안되더라,,(왜안되는지 아시는분 좀 알려주세요 ㅠㅠ)

그래서 뭐 그냥 str.split, str.replace 랑 recvuntil() 가지고 겨우겨우 짤라서 sympy로 solve() 해놨는데 갑자기 어느순간 막히길래 뭐지 하고보니... 중근이 있었다 (~~젠장~~)

그래서 중근을 판별은 어캐하지 부터 시작해서 한 2시간동안 고민을 했다. 
근데 생각해보니 인수분해를 하면 제곱항이 나올거고 그 제곱항 앞에 숫자가 뭔지만 찾으면 되는것 아닌가!! 

그래서 factor() 함수로 인수분해 한걸 또 스플릿 스플릿 해서 중근을 구하고 넣어주는 로직을 만들어서 솔브하니 또 문제가 있었다. 플래그가 안나오는거,,,

그래서 와이어샤크로 뜯어도 보고 오만짓 다해봤는데 안나와서 뭐지 하다가 쉘 따는건가 싶어서 interactive() 함수로 쉘을 따서 플래그를 얻었다
![image](https://user-images.githubusercontent.com/24792377/60893533-068ccf00-a29c-11e9-8a59-b547ac6202c3.png)

Flag is `DIMI{ju5t_a_5impl3_c4lcUlati0n}`
익스코드는 여기에,,

``` python
from sympy import *
from pwn import *
import time
r = remote("ctf.dimigo.hs.kr", 8231)

x = symbols('x')
bbbb = r.recvuntil('Example) x^3 - 6x^2 + 11x - 6 = 0 >> 1, 2, 3')
time.sleep(5)
aaaa = r.recvuntil("\n\nNow Starting . . . \n\n")
# bbbbb = r.recvuntil("\n")
a = 1 
print("gofunc")
while True:
    prob = r.recvuntil(">>").decode()
    aaa = prob
    print(prob)
    prob = prob.replace('No. ', '')
    prob = prob.replace(str(a) + ')', '')
    a = a + 1
    prob = prob.replace('>>', '')
    prob = prob.replace('^', '**')
    prob = prob.replace('x', '*x')
    log.info("prob=" + prob)
    arr = prob.split(" = ")
    prob_1 = arr[0]
    prob_2 = arr[1]
    prob_1.replace("'","")
    prob_2.replace("'","")
    # eqn = Eq(prob_1, prob_2)
    aa = prob_1
    log.info('PROBLEM1 : ' + aa)
    ans = solve(aa,x)
    ans1 = ans[0]
    ans2 = ans[1]
    if len(ans) == 3:
        ans3 = ans[2]
        fans = str(ans1) +", "+ str(ans2) +", "+ str(ans3)
        log.info('ANSWER : ' + str(ans1) +", "+ str(ans2) +", "+ str(ans3))
    else:
        bb=factor(aa)
        log.info('factor :' + str(bb))
        bbb = str(bb).split(')**2')
        cccc = str(bbb[0])
        ccc1 = len(cccc)
        bbbb = str(cccc[ccc1-2]) + str(cccc[ccc1-1])
        log.info(cccc)
        fans = str(ans1) +", "+ str(ans2) + ", "+ str(bbbb) 
        log.info('PANSWER : ' + str(ans1) +", "+ str(ans2) + ", "+ str(bbbb[1]))

    r.sendline(str(fans))
    if a == 301:
    	r.interactive()
        # log.info('FLAG : ' + r.recvuntil("}").decode())

```
~~사실 씨텝용으로 대충푼거라 변수명부터 코드 자체가 엄청나게 조잡하고 비효율 적이다 (읍읍)~~

## CTFind

자바 파일이길래 그냥 뜯어봤다 (JD-GUI 는 정말 최고다)

![image](https://user-images.githubusercontent.com/24792377/60893720-697e6600-a29c-11e9-8f87-c05fc26f3c9a.png)

까서 search 하니까 바로 플래그가 나왔다 (글자크기 지못미,,)

![image](https://user-images.githubusercontent.com/24792377/60893741-726f3780-a29c-11e9-837d-d329a06f06c6.png)

Flag is `DIMI{ju5t_c0mmoN_j4va_Dec0mPil3_}`

## DIMI-Contract
<contract>
사실 문제 재목만 보고 솔리디티 (스마트 컨트렉트) 문제를 기대했다.
그러나 그냥 언더플로우 문제였다

끝나기 5분전에 봐서 끝나기 44초 전에 풀었다

![image](https://user-images.githubusercontent.com/24792377/60893951-c11cd180-a29c-11e9-8a52-522da94241ee.png)
접속 하면 요로코롬 뜬다. 일단 debt을 해야 payback이 가능하니 debt를 해보자
![image]
![image](https://user-images.githubusercontent.com/24792377/60894014-de51a000-a29c-11e9-85c6-d8474194f388.png)
그리고 payback을 크게 - 해주면
![image](https://user-images.githubusercontent.com/24792377/60893989-d42fa180-a29c-11e9-8c65-34e95008424f.png)
짠짜잔!

Flag is `DIMI{m1nu5_b4nk_cUrR:p7}`

나름 이번 씨텝도 머꿀잼이였다. 본선땐 포너블 열심히 해서 한문제라도 풀어봐야겠다 
