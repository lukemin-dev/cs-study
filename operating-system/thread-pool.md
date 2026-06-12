# Thread Pool

## 한 줄 정의

Thread Pool은 작업이 들어올 때마다 스레드를 새로 만들지 않고, 미리 준비하거나 관리되는 스레드 집합에 작업을 맡기는 방식입니다.

## 왜 필요한가

스레드는 가벼운 것처럼 보여도 생성과 관리 비용이 있습니다. 요청이나 접속이 들어올 때마다 무제한으로 새 스레드를 만들면 메모리 사용량과 컨텍스트 스위칭 비용이 커질 수 있습니다.

```text
요청마다 new Thread()
-> 단순함
-> 접속자가 많아지면 리소스 부담 증가

Thread Pool
-> 작업 제출과 스레드 관리 분리
-> 제한과 재사용을 설계하기 쉬움
```

## ExecutorService

Java에서는 `ExecutorService`로 Thread Pool을 다룰 수 있습니다.

```java
ExecutorService clientPool = Executors.newCachedThreadPool();
clientPool.submit(new ClientSession(this, socket));
```

`submit()`은 실행할 작업을 Thread Pool에 맡깁니다.

## Cached Thread Pool

`Executors.newCachedThreadPool()`은 필요할 때 스레드를 만들고, 사용하지 않는 스레드는 일정 시간 뒤 정리합니다.

장점:

- 간단하게 여러 작업을 동시에 처리할 수 있습니다.
- 짧게 끝나는 작업이 많을 때 편합니다.

주의점:

- 최대 스레드 수가 사실상 제한되지 않아 접속자가 너무 많으면 위험할 수 있습니다.
- 실무 서버에서는 고정 크기 Pool이나 직접 설정한 `ThreadPoolExecutor`를 고려합니다.

## 내 프로젝트와 연결

`multichat-java`에서 서버는 클라이언트 접속을 받으면 `ClientSession` 작업을 Thread Pool에 맡깁니다.

```java
Socket socket = serverSocket.accept();
clientPool.submit(new ClientSession(this, socket));
```

각 `ClientSession`은 클라이언트 한 명의 메시지를 계속 읽습니다.

```text
client 1 -> ClientSession 1 -> Thread Pool
client 2 -> ClientSession 2 -> Thread Pool
client 3 -> ClientSession 3 -> Thread Pool
```

이 구조 덕분에 한 클라이언트가 입력을 안 하고 있어도 다른 클라이언트의 메시지를 처리할 수 있습니다.

## 한계와 개선

현재 구조는 학습용으로 단순하고 이해하기 좋습니다. 하지만 접속자가 많아지는 실무 서버라면 아래를 고민해야 합니다.

```text
최대 접속자 수 제한
고정 크기 Thread Pool
대기 큐 크기 제한
idle timeout 설정
서버 종료 시 graceful shutdown
Java NIO 기반 비동기 처리
```

## 30초 면접 답변

Thread Pool은 작업마다 스레드를 새로 만들지 않고 관리되는 스레드 집합에 작업을 맡기는 방식입니다. 스레드 생성 비용과 리소스 사용을 제어하기 위해 필요합니다. 제 `multichat-java` 프로젝트에서는 클라이언트가 접속할 때마다 `ClientSession`을 만들고 `ExecutorService`에 제출해 여러 클라이언트를 동시에 처리했습니다. 다만 현재는 cached thread pool이라 접속자가 많아지면 스레드 수 제한이 약하므로, 실무에서는 고정 크기 Pool이나 NIO 방식을 검토할 수 있습니다.

## 꼬리질문 대비

### Thread Pool을 쓰면 무조건 안전한가요?

아닙니다. Pool 크기, 큐 크기, 작업 시간, 예외 처리, 종료 처리를 함께 설계해야 합니다. 잘못 설정하면 요청이 밀리거나 스레드가 과도하게 늘어날 수 있습니다.

### 채팅 서버에서 스레드 방식의 단점은 무엇인가요?

클라이언트 수가 많아질수록 스레드 수와 컨텍스트 스위칭 비용이 증가합니다. 많은 연결을 처리해야 하는 서버라면 Java NIO 같은 비동기 I/O 모델을 고려할 수 있습니다.
