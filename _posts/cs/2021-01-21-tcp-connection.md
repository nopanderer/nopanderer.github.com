---
layout: post
title: "[네트워크] TCP 연결 설정"
categories: cs
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## TCP 연결 설정 과정 (3-way handshaking)

- Sequence Number(Seq.Num) : 패킷의 순서 번호
- Acknowledgement Number(Ack.Num) : 다음 수신 시 받기를 기대하는 시퀀스 넘버

![3-way handshake](/assets/img/3way-handshake.png)

1. [Client → Server] SYN : 클라이언트 측 초기 시퀀스 넘버인 500으로 연결 요청(SYN)을 보냄
2. [Server → Client] SYN+ACK : 클라이언트에게 시퀀스 넘버가 500인 연결 요청을 받고 서버 측 초기 시퀀스 넘버인 250과 다음에 받기를 기대하는 시퀀스 넘버(Ack.Num)인 501으로 확인 응답(ACK)과 연결 요청(SYN)을 보냄
3. [Client → Server] ACK : 서버에게 시퀀스 넘버가 250인 확인 응답과 연결 요청을 받고, 연결 요청에 대한 확인 응답(ACK)을 보냄

## TCP 연결 종료 과정 (4-way handshaking)

![4-way handshake](/assets/img/4way-handshake.png)

1. [Client → Server] ​FIN+ACK : 서버 측에 연결을 종료하겠다는 FIN 패킷을 보냅니다.
2. [Server → Client]​ ACK : 클라이언트에게 받은 FIN 패킷에 대한 확인 응답(ACK)을 보냅니다.
3. [Server → Client]​ FIN+ACK : 클라이언트에게 데이터를 마저 받은 후에 클라이언트 측에 연결을 종료하겠다는 FIN 패킷을 보냅니다.
4. [Client → Server] ​ACK : 서버에게 받은 FIN 패킷에 대한 확인 응답(ACK)를 보내고 일정 시간 후에 종료합니다.

이때 일정 시간 대기한 후 종료하는 이유는 마지막 확인 응답(ACK)이 상대에게 전송됨을 보장하기 위함이다. (서버가 ACK를 받지 못하면 서버는 종료를 하지 못하므로, 클라이언트가 마지막 확인 응답을 보낸 후 서버 측에서 이 응답을 받지 못할 경우 timeout. FIN+ACK을 다시 보내게 된다.)

## 참고

- <https://blog.naver.com/is_king/221570764551>