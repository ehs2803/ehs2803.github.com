---
title:  "[네트워크] 소켓통신 개념과 간단한 예제"
date: 2021-07-20 21:19:00
categories:
  - 네트워크
  - 한이음[21_HI023]
tags:
  - 네트워크
  - 한이음
  - 21_HI023
  - 소켓
  - socket
---

# 소켓

소켓은 1982년 BSD(Berkeley Software Distribution) UNIX 4.1에 처음 소개되었으며 현재 널리 사용되는 것은 1986년의 BSD UNIX 4.3에 개정된 것이다.

소켓은 소프트웨어로 작성된 추상적인 개념의 통신 접속점으로 두 프로그램이 네트워크를 통해 서로 통신을 수행할 수 있도록 양쪽에 생성되는 링크의 단자이다.

소켓은 응용 프로그램에서 TCP/IP를 이용하는 창구 역할을 하며 응용프로그램과 소켓 사이의 인터페이스 역할을 한다.

소켓(socket)은 네트워크로 데이터를 내보내거나 데이터를 받기 위한 실제적인 창구 역할을 한다. 프로세스가 데이터를 보내거나 받기 위해서는 반드시 소켓을 열어서 소켓에 데이터를 써서보내거나 소켓으로부터 데이터를 읽어들여야 한다.

즉 소켓은 떨어져 있는 두 호스트를 연결해주는 도구로써 인터페이스의 역할을 하는데 데이터를 주고 받을 수 있는 구조체로 소켓을 통해 데이터 통로가 만들어진다. 이러한 소켓은 역할에 따라 서버 소켓, 클라이언트 소켓으로 구분된다.

<br>
<br>

# 소켓 종류

스트림(TCP)
* 양방향으로 바이트 스트림을 전송, 연결 지향성
* 오류 수정, 전송처리, 흐름제어 보장
* 송신된 순서에 따라 중복되지 않게 데이터를 수신해 오버헤드가 발생
* 소량의 데이터보다 대량의 데이터 전송에 접합

데이터그램(UDP)
* 비연결형 소켓
* 데이터의 크기에 제한이 있음
* 확실하게 전달이 보장되지 않음. 데이터가 손실돼도 오류가 발생하지 않음.
* 실시간 멀티미디어 정보를 처리하기 위해 주로 사용된다. ex)전화


위와 같은 특징으로 TCP보다 UDP가 통신 속도가 더 빠르다.

<br>
<br>

# 소켓의 구성

* 프로토콜
프로토콜은 원래 외교상의 언어로 의례나 국가간에 약속을 의미한다. 통신에서는 어떤 시스템이 다른 시스템과 통신을 원활하게 수용하도록 해주는 통신 규약, 약속이다.

* IP주소
전세계 컴퓨터에 부여된 고유의 식별 주소

* 포트넘버
포트(port)는 네트워크 상에서 통신하기 위해서 호스트 내부적으로 프로세스가 할당받아야 하는 고유한 숫자이다. 한 호스트 내에서 네트워크 통신을 하고 있는 프로세스를 식별하기 위해 사용되는 값으로 같은 호스트 내에서 서로 다른 프로세스가 같은 포트넘버를 가질 수 없다. 즉 같은 컴퓨터 내에서 프로그램을 식별하는 번호이다.

<br>
<br>

# 소켓통신 흐름

![socket](/img/socketStream.PNG?raw=true)

#### 서버(server)
클라이언트 소켓의 연결 요청을 대기하고, 연결 요청이 오면 클라이언트 소켓을 생성해 통신이 가능하게 한다.

1. socket()함수를 통해 소켓을 생성
2. bind()함수로 ip와 port 번호를 설정
3. listen() 함수로 클라이언트의 접근 요청에 수신 대기열을 만들어 몇개의 클라이언트를 대기 시킬지 결정
4. accept()함수를 이용해 클라이언트와 연결을 기다림


#### 클라이언트(client)
실제로 데이터 송수신이 일어나는 것은 클라이언트 소켓이다.

1. socket()함수로 가장먼저 소켓을 연다.
2. connect() 함수로 통신 할 서버의 설정된 ip와 port 번호에 통신을 시도
3. 통신 시도 시 서버가 accept()함수를 통해 클라이언트의 socket descriptor를 반환
4. 이를 통해 클라이언트와 서버가 서로 send(), write()를 하며 통신 (반복)

<br>
<br>

# 소켓통신 간단한 예제 - 파이썬

```python
import socket

HOST = '' #all available interfaces
PORT = 8888

#open Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print ('Socket created')

# bind to a address and port
try:
    s.bind((HOST, PORT))
except socket.error as msg:
    print ('Bind Failed. Error code: ' + str(msg[0]) + ' Message: ' + msg[1])
    sys.exit()

print ('Socket bind complete')

# Listen for incoming connections
s.listen(10)
print ('Socket now listening')

conn, addr = s.accept()
print ('Connected with ' + addr[0] + ':' + str(addr[1]))

while 1:
    # Read
    data = conn.recv(1024)
    if data=="exit":
        break

conn.close()
s.close()
```
server 

<br>

```python
import socket

HOST = '192.168.0.28' 
PORT = 8888

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))

msg = "abc"
s.send(msg.encode(encoding='utf_8', errors='strict'))

s.close()
```
client

<br>
<br>


# SOCKET통신과 HTTP 통신 비교

#### SOCKET 통신
server와 client가 특정 port를 통해 실시간으로 양방향 통신을 하는 방식

* server와 client가 계속 연결을 유지하는 양방향 통신이다.
* server와 client가 실시간으로 데이터를 주고받는 상황이 필요한 경우에 사용
* 실시간 동영상 streaming이나 온라인 게임 등과 같은 경우에 자주 사용

#### HTTP 통신
client의 요청(request)이 있을 때만 서버가 응답(response)해 해당 정보를 전송하고 곧바로 연결을 종류하는 방식

* client가 요청을 보내는 경우에만 server가 응답하는 단방향 통신.
* server로부터 응답을 받은 후에는 연결이 바로 종료
* 실시간 연결이 아니고 필요한 경우에만 server로 요청을 보내는 상황에 유용
* 요청을 보내 server의 응답을 기다리는 어플리케이션의 개발에 주로 사용.
