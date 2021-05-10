---
title: 하이퍼레저 톺아보기
date: 2019-04-12 10:03:39
tags: 
- dev
- blockchain
- hyperledger
categories: Blockchain
---

![](https://steemitimages.com/640x0/https://cdn.steemitimages.com/DQmSbR2Gf2uBcT82FXsznHhUN4Wvb9MzDUp8NFvcaJA8cYZ/hyperledgerlogo.png)


## 하이퍼레저란?

리눅스 재단에서 주관하는 블록체인 오픈소스 프로젝트이다.
금융, IoT, 물류, 제조, 기술 산업 등 여러 산업에 걸쳐 응용 가능한 블록체인 기술을 만드는 것이 목표로 하고 있다.
하이퍼레저가 특이한 점은

1. 프라이빗 블록체인 플랫폼으로서 기업 비즈니스를 구현하기에 적합한 환경이라는 점,
2. 금융에 특화된 타 플랫폼과 달리 여러 산업에 범용적으로 도입 가능한 기술 표준을 제시한다는 점이다.

## 용어정리


합의 알고리즘 : PBFT
PFBT = 참여자의 2/3 이 동의를 해야만 유효한 거래로 인정.

---

Orderer : 블록을 만들 수 있는 노드
4명 이상의 오더러가 필요. (이론적으로는 3명 이상이면 무관. 그러나 하나가 죽으면..OTL)
참여자 = Validation Node = 체인코드와 원장을 저장하고있는 노드
검증(보증)노드는 ChainCode를 도커에 배포해서 검증.

---

Network : 하이퍼레저 페브릭

Channel : 논리적인 통신선로

CA : 인증서를 관리하는 조직

MSP : Membership Service Provider , 하이퍼레저 접속권한 관리.

---

Peer : 원장을 유지하고 체인을 유지시키는 네트워크 엔디티

- Commit Peer : Validation 을 수행하는 피어 (World State 에 데이터를 업데이트)
- Leader Peer : Orderer 에게 검증된 블록을 수신해 commiter Peer에게 브로드캐스팅
- endorsing(보증) peer : 애플리케이션으로부터 트랜잭션 요청을 수신하고 검증 해서 전달

---

Chaincode : 하이퍼레저의 스마트 컨트렉트

Ledger : 원장 → State로써 저장.

Transaction : 체인코드 디플로이(원장초기화) / invoke (원장 쓰기)


## 공유원장 - World state


현재 World State 는 원장의 모든 자산에 대한 최신값을 저장
프로그램이 원장의 현재상태 값을 쉽게 이용할 수 있게 함

- Couch DB
- Level DB
사용 가능 (둘다 NoSQL)

World State를 비트코인의 경우에는 사용불가, 돈이라서.

상태 DB는 단순히 체인의 커밋된 트랜젝션에 대한 인덱싱 뷰

- 체인에서 언제든지 재성성 가능
- 상태 DB는 새로운 트랜젝션이 수락되기 전 피어가 시작될때 자동복구
- 기본상태 데이터베이스는 LevelDB (Couch로 변경가능)

트렌젝션 -> 저장하는 행위
읽는것 != 트렌젝션

Write Speed 보다 Read Speed가 필요함, 기업체의 Case.

Level DB 와 Couch DB 는 유사함
하나에 키에 여러가지 데이터 포함 은 Couch DB,
Key Value 형태로 저장은 Level DB

## 공유원장 - 블록체인과 블록

---

하이퍼레저 블록의 경우 **헤더/데이터/메타데이터** 로 구성됨

### 헤더 구조

- Block Number
- hashvalue
- previous hashvalue

### 트렌젝션 구조

- H: ChainCode 의 이름 및 버전 정보
- S: 트렌젝션 발생시킨 유저(SW)의 암호화 서명
- P: ChainCode 의 Payload (Argument)
- R: 체인코드의 출력으로 트랜젝션의 유효성 확인 시 장부의 적용되며 World State에 업데이트(실행 될 수 있는 체인 코드인지 검증 Like EVM) (보증노드에서 검증)
- E: 보증정책을 만족시키기 충분한 각 기관의 서명된 응답 목록

### Hyperledger 은 최종 상태를 DB에 저장하는 방식!!

## ChainCode

---

체인코드는 트렌젝션을 실행하고 원장내에 저장된 자산의 상태를 수정하는 로직이 포함된 프로그램
하이퍼 레저의 체인코드는 `Go,NodeJS,Java` 로 작성가능.
**시스템 (즉 코어) 체인코드는 무조건 GO로 작성 되어야함**

체인코드는 hyperledger Fabric 네트워크의 비즈니스 로직으로 사용되며 네트워크 내에서 자산을 조작하는 방법을 지시함.

참여자 = Validation Node = 체인코드와 원장을 저장하고있는 노드
검증노드는 ChainCode를 도커에 배포해서 검증.

**체인코드의 실행 = 트렌젝션**

체인코드가 실행되고 나면, 실행의 완료가 될 경우 블록이 하나 추가되고, World State 가 업데이트 됨

보증노드는 별계의 노드가 아닌 동시의 역할을 보유 할 수 있음.

## Endorsement policy

---

- 원장의 일관성 보장
- Peer의 체인코드 저장시 Endorsement Policy를 설정.
- Endorsement peer은 클라이언트에서 트렌젝션이 보내지면 Endorsement Policy를 기반으로 트렌젝션을 검증하고 결과에 자신이 서명을 한 뒤에 클라이언트에게 회신.

## 기업용 블록체인을 위한 보안 요구사항

---

- 신원관리
    - 업무사용을 위한 참가자의 신원확인 및 신원인증
- 트렌젝션의 기밀성
    - 트렌젝션이 암호화되어 일반사용자에게 보이지않도록함
- 재생 공격 대책 (Replay)
    - 과거의 트렌젝션을 복사하여 재전송하는 공격이 방지되어야함
- 개인정보보호
    - 트렌젝션 발생자의 익명화
    - 동일한 사용자가 발생한 여러 트랜젝션들은 연관성이 없어야함
- 엑세스 제어
    - 스마트컨트렉트(체인코드)를 실행할 수 있는 사람을 재한(초기화,함수실행,데이터 참조 등등)
- PKI 인증서와 전자서명
    - 사용자의 신원을 확인함
    - 개별트랜젝션은 익명의 인증서를 통해 신원을 숨긴체 인증 할 수 있음.

## 인증서

---

- ECERT(Enrollment Certificate)
    - 사용자의 원장 접근을 위한 인증서. 검증노드/비검증노드/사용자에서 발행
- TCERT(Transaction Certificate)
    - 트랜젝션마다 발행되는 단기적으로 사용하는 인증서, 트렌젝션 마다 다른 Tcert를 사용하면 여러 트렌젝션이 동일한 사용자인걸 숨길 수 있음.
- TLS Certificate

## Fabric CA

---

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/83966d00-e9ca-462e-b960-bd74a1ba15ba/IMG_3132_2.jpg?AWSAccessKeyId=ASIAT73L2G45MVA5LEPG&Expires=1555116971&Signature=PJ1YleZxmZt7BmflySmMiZxwGpo%3D&response-content-disposition=filename%20%3D%22IMG_3132_2.jpg%22&x-amz-security-token=AgoJb3JpZ2luX2VjEND%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJGMEQCIAyELkLJJFN00YIbjXCnGZ3Zcmc9tsTgY6XpgfZdhnlfAiBN%2BOe%2FKqgUTDSELMWZGXHf4lpn%2BhDBRJ%2BaTT1VH5TPTyrjAwiZ%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDI3NDU2NzE0OTM3MCIM8oCbMgPYPPRnh9XPKrcDhEEQgQSKg9qjgYfwPn%2Brvx60AKAoxzGc3Yiayv%2B0o7ew6WTiAqjJMBJQ2EfbIbNrwioXSz9diAlsUiEprf3tHOO7TFfZ%2B48iGUyF3d9iG0anYQXH0s8p12fjNRxv7HUEzESfyw6%2BXu%2FYQS4KLMNAb9EGK5A4Hk%2FSyG%2F3ThO186tAHQlPS6K99l5K2bFuEonlua3o4kLB9%2F4utASRGj%2F8%2F%2BjmkChB80SKyf1xYWaf5pIaBiDM5v31r1ByYU%2FPo%2BPiweHmr2fNl4t9q1vWn4mw0q%2F8HWj0LaBnfgUlQ9jKGEOxoSAVGAhRRcOLf1PnhPU%2FkYRjNxVb3F8annLVUL0xFW0g1%2FMgO5QHyMNbpG2lA9lO5NmlhY7vExQz22zjaYE7TiuZgusnD6COd4xp%2Bj%2BhLNYpdkGQjyEhkzzwFBEyDSP0PfW3h0gLgA5C5evaRgDOYxVBC%2BR%2FAAEEdFw1uS9J4rZ2gzO%2BW5fwX140mWzg%2FggUWYpT09B4ez6ZbdJQtnqeIh2gzIUt22dd1TOoik3lAcNpeSYxjeFebbUA5uwnLG%2BE2WfprhxCUTkVLW3Uc7AzY4KI5kD2SDCip7%2FlBTq1AflAtpT7yhKSHIEWOypbTIs6j7O%2BnL60HyUyQSf%2B5skfSsVoQmlF8Bc%2BwYleLqCkEGFoMDkoeluhHUgOAl%2BZ6vLCdd7fl6MYhF%2F9BtfwrAuxBvsU32ILVw1R9CjGeGYpWgn2HZYR7O0%2Bf5iKSsweuNakjS8k5MJlU609Fvnp9CWMddBHwjmGzcweMdMot9CQTpXXHYKJgHAx7pi%2FM8uwabtKqy3xVRnIPD80Xk21Vd0%2BQKWuNAM%3D)

## MSP (Membership Service Provider)

---

맴버십 서비스 공급자는 신원을 확인하고 인증하고 네트워크 엑세스 룰을 정의하는 구성요소.

사용자 관리, 네트워크 가입을 요청하는 클라이언트 인증.

사용자 인증서를 확인하고 취소할 수 있는 플러그 가능한 CA를 사용

⇒ 인증서 해지 목록도 관리할 필요성. Cause: 기업용이라.

Fabirc CA + External CA 사용가능 

## Setup To Develop

---

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/cef915ab-20c8-4306-89a9-87bf00958cc0/IMG_3133.jpg?AWSAccessKeyId=ASIAT73L2G45EMMXBA4Z&Expires=1555117044&Signature=eVvFag7zaCZcfvnyf2L30mwJ%2BEo%3D&response-content-disposition=filename%20%3D%22IMG_3133.jpg%22&x-amz-security-token=AgoJb3JpZ2luX2VjEND%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJGMEQCIH5tQzUqFcBOI21RI1knlxMtsSTglVfrHL7ZbFGTPuERAiByMI98H83%2FWpscUPoEoo3q2Dhfol5qOJUJobwbzzGDqirjAwiZ%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDI3NDU2NzE0OTM3MCIMpP9lCdMwFp89zCIqKrcD%2Fy%2Bf9DgjHSugaYHvW9pIYmq7MkjfrP8lPOMZ3ZdPv0gbpE%2FqvRcBT6G%2B6MYIgbRnonFVz9pnM%2BV%2FjqBjnc5%2BIgnL91t7GgCN2x4mP1tSD0nllZT%2Bxi%2BvVtuYFD%2FWVNKuFE0gmgCXA9COgKxHxHx2arU76aUgDZQNjfKl%2BoReJTzKxSPxNSuiyY0HPL1MAc8FNzulCMusSNLJGpfllfoYb38Q8pkHaylG4i2Df%2BW7bBtcf8FXc6xS%2FxbVfs3u3eTqw96uomhn2SSvgsbormqM2goyOoRHBJdrrPcMfhUoj5jTs3TuLi6It3TcuXhfS8vvHzVeP4zTP6HAl%2FmFJQgWrFM3Rdu7NxqLZOs%2Fb%2B42nmty4mLOiXnFc%2FbRZjj%2BHvWyXKzByOXJppECB1PZs%2FolTkVSPdIyIfziQJIyKL%2BV7mkQ4loV28IRL3rggOoydHUUGkcmatMCklaKjowPU8y9tWiojik3W%2FncPYBvjk9%2FytRnBFPT0VXyV1m2YByaZX4vbcuNIFIW0u%2FU4bxUJlheEDJALwCgxLUAXFfDq2M%2Fz90i89KCb33r5oSZ2NYoolthF7icJ6UGzjDBmr%2FlBTq1AcPo6BRWC6no8v1TNbjj0WK%2BSg7CZ1atNMqSEg5C6%2Bdd9kwK0GmOnAI9XItL6yZs3pdiMipsiUmqj4a%2Fbw%2Fi0zMmZ5pgbQSlscee0w877q1XClLTZlcshEENRL%2BruVgIi%2B6VwLEOv4wSpOsG%2ByPgEGxQERLdiplrUIYe4Kzy%2FUVT4dey%2FMZ1A9jJbloOm5WVpwD9bWPLXHk2Xf4yvJ%2B9Ql7KY3gKkkHreHZF%2FYbLiWJ2jLZL480%3D)

MYSQL = 모니터링 DB

CouchDB/LevelDB ⇒ NoSQL = KVS DB

Postgre SQL = CA관리

---

Kafka/Zookeeper ⇒ 컨센서스 

Docker ⇒ 검증노드의 검증을 위해.

---

Go → System ChainCode

Node.js → Application ChainCode

PostMan → Rest API TEST

---

# 네트워크 구성

---

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/258ac2e1-ed88-48d0-8331-dd35a1c3d86a/Untitled.png?AWSAccessKeyId=ASIAT73L2G45CT3IC2JO&Expires=1555117090&Signature=Z0SLFwBUxO7n5trQbwXA%2FMeMV9s%3D&response-content-disposition=filename%20%3D%22Untitled.png%22&x-amz-security-token=AgoJb3JpZ2luX2VjEND%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJHMEUCIFIa%2B2dGOPY6BksQTKDgQlJYATWxFhQhTy%2BVk6pTp%2B6OAiEA6HCm1zmewMqYPo1p2vEmFXPA7Ukp5ilACdSjiI784b8q4wMImf%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAAGgwyNzQ1NjcxNDkzNzAiDL8kfmRld%2FPEjhSKPCq3A500L3YWFZtu13a4sEQ9kfXfkAKrPKyak5%2Fuv6bL3blHHtU%2BtCqhEyh89qyzHAoCMwN6XOxzOOEKq3OROlLZVVZepUS6if%2BRkanU%2BP0K6ZrHAvbr6XWHd2Rb%2FmLBSGk4UUOS7Nj1%2B1Y%2BPfI3D2p%2FgLBlt1LvfNfPiNRK5i%2FLdnT5wnBLVuTKfUTLXUzIi346EHZOws5duN2Bi8sLxm%2BGz0NUxl2SnCr%2BizdnMxRLJN2s1dRmmrxh3wDQ5VLSuY05YKz%2BwFqcfVfah6j4JhqtKR1YqD0b%2FvZHP%2BCKgeOB5VqiHwYM%2BYyxvZiAPgKsORlUqxFPbx6P5RPZeYCKDxg4DXkOonp6DzojFZFtAZilMHjEtN%2FEZaFTgTvKpF%2FOCIBxKSU%2BIiaEg6KcgefaeWtQHnwnjoQI7rM%2BfC7QXKkACvaBbLoVJu1YaWSRZMWGLUn5tieiEgOCuELy7YvLG1ugnaG8VPSXeXzUa4Yy8PVxF85ZzFRX6K3c%2Fp%2BvtjaRRFbgEdt5uI634a0hhKCQ4osa7CW3FeJkwPPXPPVp%2FsDnwItrSny4X9eQbGi6LcUaTtg2vsPLXT50f8Aw2pu%2F5QU6tAGA1sWb5z%2BHvrI1czYiu8%2F68%2Bn%2FfWyJJSYqZZdZRifj8l4qzz8Rln9N3k6RzMyGuTtRpyEutBDLtVSQuPk4QL6qca8OBD2HUeD4SphFfPnjeiLrmnOg04FcLlnI4Eos%2FCOviFOjTLo6zS7R%2BvHevYXVPuqY8l0Ddi%2BM5iy%2Bijft8fZQ9Exi75%2BJR8WxW%2BS%2BsdZ2RW%2FFYaWta3ZNGoVAP71TUbT6A%2B5wTA0Cc%2FOARhFC8PjWCN0%3D)

실제 환경 구축을 위해 Docker 활용
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjcyNjUzMDEyLDU3MTM3OTQzMCwxNTg0Mj
U5MDQ1XX0=
-->