# Multichat Java Q&A

이 문서는 `multichat-java` 프로젝트를 면접에서 설명하기 위한 질문/답변 모음입니다.

## 1. 이 프로젝트를 한 문장으로 설명해보세요.

Java Socket과 Thread Pool을 사용해 여러 클라이언트가 동시에 접속해 메시지를 주고받을 수 있는 터미널 기반 멀티 채팅 서버입니다.

## 2. 왜 TCP를 사용했나요?

채팅 메시지는 누락되거나 순서가 바뀌면 대화 흐름을 이해하기 어렵습니다. 그래서 빠르지만 손실 가능성이 있는 UDP보다, 순서와 재전송을 보장하는 TCP가 더 적합하다고 판단했습니다.

다만 TCP는 메시지 경계를 보장하지 않기 때문에, length-prefix 방식으로 메시지 단위를 직접 구분했습니다.

## 3. TCP가 메시지 경계를 보장하지 않는다는 말은 무슨 뜻인가요?

클라이언트가 메시지를 한 번 보냈다고 해서 서버가 그 메시지를 한 번의 read로 그대로 받는다는 보장이 없다는 뜻입니다.

```text
보낸 데이터: hello world

받는 경우 1:
hel
lo world

받는 경우 2:
hello world + 다음 메시지 일부
```

TCP는 메시지 단위가 아니라 바이트 스트림 단위로 동작하기 때문에 애플리케이션이 직접 메시지를 구분해야 합니다.

## 4. 메시지 프레이밍은 어떻게 처리했나요?

각 메시지 앞에 payload 길이 4바이트를 먼저 보내는 length-prefix 방식을 사용했습니다.

```text
[payload length 4 bytes][payload bytes]
```

수신 쪽에서는 먼저 4바이트를 읽어 payload 길이를 구하고, 그 길이만큼 정확히 읽은 뒤 `Message` 객체로 복원합니다.

## 5. delimiter 방식 대신 length-prefix를 사용한 이유는 무엇인가요?

delimiter 방식은 메시지 끝에 `\n` 같은 구분자를 붙이는 방식이라 구현은 쉽습니다. 하지만 메시지 본문에 구분자가 들어올 경우 escaping을 고민해야 합니다.

length-prefix 방식은 먼저 길이를 알고 읽기 때문에 본문에 줄바꿈이나 특수문자가 있어도 메시지 단위를 안정적으로 구분할 수 있습니다.

## 6. 왜 클라이언트마다 ClientSession을 만들었나요?

클라이언트마다 연결 상태, 닉네임, 입력 스트림이 다르기 때문입니다. `ClientSession`이 접속자 한 명을 담당하면 서버 전체 관리와 개별 접속자 처리를 분리할 수 있습니다.

```text
ChatServer
-> 서버 포트 열기, 접속자 목록 관리, broadcast

ClientSession
-> 클라이언트 한 명의 JOIN/CHAT/LEAVE 처리
```

## 7. 왜 Thread Pool을 사용했나요?

여러 클라이언트를 동시에 처리하기 위해서입니다. 한 클라이언트가 메시지를 보내지 않고 기다리는 동안 서버 전체가 멈추면 안 됩니다.

`ChatServer`는 접속을 받으면 `ClientSession`을 `ExecutorService`에 제출합니다.

```java
clientPool.submit(new ClientSession(this, socket));
```

이렇게 하면 각 클라이언트의 메시지 읽기 작업이 별도 스레드에서 처리됩니다.

## 8. 현재 Thread Pool 구조의 한계는 무엇인가요?

현재는 `newCachedThreadPool()`을 사용합니다. 학습용으로는 단순하지만, 접속자가 매우 많아지면 스레드 수가 많이 늘어날 수 있습니다.

실무적으로 개선한다면 아래를 고려할 수 있습니다.

```text
고정 크기 Thread Pool
최대 접속자 수 제한
대기 큐 제한
graceful shutdown
Java NIO 기반 비동기 처리
```

## 9. 접속자 목록은 어떻게 관리했나요?

접속자가 들어오면 `register()`, 나가면 `unregister()`로 `sessions`에 추가/삭제합니다.

```java
private final Set<ClientSession> sessions = ConcurrentHashMap.newKeySet();
```

여러 클라이언트 세션 스레드가 동시에 접근할 수 있으므로 일반 `HashSet` 대신 동시성에 안전한 Set을 사용했습니다.

## 10. broadcast는 어떤 방식으로 동작하나요?

현재 접속 중인 모든 `ClientSession`을 순회하며 메시지를 보냅니다.

```text
접속자 n명
-> n명에게 send()
-> O(n)
```

채팅방 하나에 접속자가 적을 때는 충분하지만, 접속자가 많아지면 브로드캐스트 비용이 커질 수 있습니다.

## 11. send()에 synchronized를 붙인 이유는 무엇인가요?

같은 클라이언트 Socket에 여러 스레드가 동시에 메시지를 쓰면 바이트가 섞일 수 있습니다. 그래서 한 세션에 대한 전송은 한 번에 하나만 실행되도록 `send()`를 `synchronized`로 만들었습니다.

## 12. `/users` 명령어는 어떻게 동작하나요?

`ClientSession`이 받은 채팅 메시지의 body가 `/users`인지 확인합니다. `/users`라면 전체 broadcast를 하지 않고, 요청한 클라이언트에게만 현재 접속자 목록을 `SYSTEM` 메시지로 보냅니다.

```text
클라이언트가 /users 입력
-> ClientSession이 명령어 감지
-> ChatServer.connectedNicknames() 호출
-> 요청자에게만 SYSTEM 메시지 전송
```

## 13. MessageType을 enum으로 만든 이유는 무엇인가요?

문자열로 메시지 타입을 직접 다루면 오타가 생겨도 컴파일 시점에 잡기 어렵습니다. enum을 사용하면 가능한 메시지 종류를 제한할 수 있고, 코드에서 `JOIN`, `CHAT`, `LEAVE`, `SYSTEM`, `ERROR`처럼 명확하게 표현할 수 있습니다.

## 14. 이 프로젝트의 한계는 무엇인가요?

현재는 학습용 터미널 채팅 서버라 기능과 운영 요소가 제한적입니다.

```text
인증 없음
채팅방 분리 없음
귓속말 없음
메시지 저장 없음
접속자 수 제한 없음
서버 graceful shutdown 부족
NIO 기반 확장성 부족
```

## 15. 다음에 개선한다면 무엇을 하겠나요?

먼저 중복 닉네임 방지와 `/whisper` 기능을 추가해 명령어 처리 구조를 개선하고 싶습니다. 그 다음 채팅 로그 저장을 추가하면서 DB 트랜잭션과 인덱스까지 연결할 수 있습니다. 규모를 키운다면 fixed thread pool이나 Java NIO 기반 구조로 바꾸는 것도 검토할 수 있습니다.

## 16. 면접에서 강조할 포인트

```text
1. TCP Socket 기반 서버/클라이언트 구현
2. TCP 메시지 경계 문제를 length-prefix로 해결
3. Thread Pool을 통한 다중 클라이언트 처리
4. ConcurrentHashMap.newKeySet()을 통한 접속자 목록 관리
5. synchronized send()로 소켓 쓰기 충돌 방지
6. Message/MessageCodec/MessageType으로 프로토콜 책임 분리
7. /users 명령어 확장 경험
```

## 17. 1분 답변

`multichat-java`는 Java Socket과 Thread Pool을 사용해 여러 클라이언트가 동시에 접속할 수 있는 터미널 기반 채팅 서버입니다. 채팅 메시지는 누락되거나 순서가 바뀌면 안 되기 때문에 TCP를 사용했습니다. 다만 TCP는 스트림 기반이라 메시지 경계를 보장하지 않기 때문에, 각 메시지 앞에 payload 길이 4바이트를 붙이는 length-prefix 프로토콜을 구현했습니다. 서버는 `ServerSocket`으로 접속을 받고, 각 연결은 `ClientSession`으로 분리해 `ExecutorService`에서 실행합니다. 접속자 목록은 여러 스레드가 접근하므로 `ConcurrentHashMap.newKeySet()`으로 관리했고, 같은 소켓에 동시에 쓰는 문제를 막기 위해 `send()`를 동기화했습니다. 지금 개선한다면 중복 닉네임 방지, 귓속말, 채팅방 분리, NIO 기반 확장성을 추가로 고려하고 싶습니다.
