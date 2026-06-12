# Socket

## 한 줄 정의

Socket은 네트워크에서 두 프로그램이 데이터를 주고받기 위해 사용하는 통신 끝점입니다.

## 왜 필요한가

서로 다른 프로그램이 네트워크를 통해 데이터를 주고받으려면 연결을 만들고, 그 연결을 통해 바이트를 읽고 써야 합니다.

채팅 프로그램에서는 아래 두 프로그램이 통신합니다.

```text
ChatClient
<-> Socket 연결
ChatServer
```

## ServerSocket과 Socket

Java에서는 서버와 클라이언트가 사용하는 클래스가 조금 다릅니다.

```text
ServerSocket
-> 서버가 포트를 열고 클라이언트 접속을 기다리는 역할

Socket
-> 실제 클라이언트와 서버 사이의 연결
```

서버 쪽 흐름:

```java
ServerSocket serverSocket = new ServerSocket(port);
Socket socket = serverSocket.accept();
```

클라이언트 쪽 흐름:

```java
Socket socket = new Socket(host, port);
```

## accept()의 의미

`accept()`는 클라이언트가 접속할 때까지 기다립니다.

```text
서버 시작
-> accept()에서 대기
-> 클라이언트 접속
-> Socket 반환
-> 해당 Socket으로 데이터 송수신
```

이때 `accept()`는 blocking call입니다. 즉, 클라이언트가 접속하기 전까지 다음 줄로 넘어가지 않습니다.

## InputStream과 OutputStream

Socket은 데이터를 메시지 객체로 직접 주고받지 않습니다. 실제로는 바이트 흐름을 주고받습니다.

```text
socket.getInputStream()
-> 상대방이 보낸 바이트 읽기

socket.getOutputStream()
-> 상대방에게 바이트 쓰기
```

그래서 `multichat-java`에서는 `MessageCodec`이 필요합니다.

```text
Message 객체
-> MessageCodec.write()
-> OutputStream에 바이트 쓰기

InputStream에서 바이트 읽기
-> MessageCodec.read()
-> Message 객체 복원
```

## 내 프로젝트와 연결

`multichat-java`의 서버는 `ChatServer`에서 포트를 엽니다.

```java
try (ServerSocket serverSocket = new ServerSocket(port)) {
    Socket socket = serverSocket.accept();
    clientPool.submit(new ClientSession(this, socket));
}
```

클라이언트는 `ChatClient`에서 서버에 접속합니다.

```java
try (Socket socket = new Socket(host, port)) {
    MessageCodec.write(socket.getOutputStream(), new Message(...));
}
```

접속이 만들어진 뒤에는 `ClientSession`이 해당 Socket을 맡아 계속 메시지를 읽습니다.

## 포트와 localhost

포트는 한 컴퓨터 안에서 어떤 프로그램으로 연결할지 구분하는 번호입니다.

```text
127.0.0.1:5000
```

- `127.0.0.1`: 내 컴퓨터 자신
- `5000`: 채팅 서버가 열어둔 포트

로컬 테스트에서는 서버와 클라이언트가 같은 컴퓨터에서 실행되므로 `127.0.0.1`을 사용합니다.

## 30초 면접 답변

Socket은 네트워크에서 두 프로그램이 데이터를 주고받기 위한 통신 끝점입니다. 서버는 `ServerSocket`으로 포트를 열고 `accept()`로 클라이언트 접속을 기다립니다. 접속이 들어오면 실제 통신용 `Socket`이 만들어지고, 이 Socket의 `InputStream`과 `OutputStream`으로 바이트를 읽고 씁니다. 제 `multichat-java` 프로젝트에서는 서버가 접속자마다 Socket을 받아 `ClientSession`에 맡기고, `MessageCodec`을 통해 바이트를 채팅 메시지로 변환했습니다.

## 꼬리질문 대비

### Socket은 HTTP와 같은 건가요?

아닙니다. Socket은 더 낮은 수준의 통신 도구이고, HTTP는 요청/응답 규칙을 가진 애플리케이션 계층 프로토콜입니다. HTTP도 내부적으로는 TCP 연결 위에서 동작할 수 있습니다.

### accept()는 왜 blocking인가요?

서버는 클라이언트가 접속하기 전까지 반환할 Socket이 없습니다. 그래서 접속이 들어올 때까지 기다립니다. 여러 접속자를 처리하려면 accept 후 각 연결을 별도 스레드나 비동기 방식으로 처리해야 합니다.
