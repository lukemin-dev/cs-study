# Java Thread

## 한 줄 정의

Thread는 Java 프로그램 안에서 독립적으로 실행되는 작업 흐름입니다.

## 왜 필요한가

하나의 작업이 끝날 때까지 전체 프로그램이 멈추면 안 되는 경우가 많습니다. 서버 프로그램은 특히 여러 요청이나 연결을 동시에 처리해야 합니다.

## 핵심 개념

- `Thread`: 실행 흐름 자체
- `Runnable`: 스레드가 실행할 작업
- `ExecutorService`: 스레드를 직접 만들지 않고 작업을 제출하는 실행 관리 도구
- `synchronized`: 동시에 접근하면 안 되는 영역을 보호
- `volatile`: 여러 스레드에서 값 변경이 보이도록 하는 키워드

## 내 프로젝트와 연결

`multichat-java`에서 서버는 클라이언트가 접속할 때마다 `ClientSession`을 생성합니다.

```java
clientPool.submit(new ClientSession(this, socket));
```

`ClientSession`은 `Runnable`을 구현합니다.

```java
final class ClientSession implements Runnable
```

즉, 클라이언트 한 명을 처리하는 작업을 Thread Pool에 맡기는 구조입니다.

## 왜 Thread Pool인가

클라이언트가 접속할 때마다 무제한으로 `new Thread()`를 만들면 관리가 어렵고 리소스 부담이 커질 수 있습니다.

`ExecutorService`를 사용하면 작업 제출과 스레드 관리를 분리할 수 있습니다.

```text
직접 Thread 생성
-> 단순하지만 관리 어려움

ExecutorService
-> 작업 제출과 실행 관리 분리
```

## synchronized send()

`ClientSession`의 `send()`는 `synchronized`입니다.

```java
synchronized void send(Message message) throws IOException
```

한 클라이언트 소켓에 여러 스레드가 동시에 메시지를 쓰면 바이트가 섞일 수 있습니다. 그래서 같은 세션에 대한 전송은 한 번에 하나씩 처리되도록 막습니다.

## volatile

`nickname`, `registered`는 `volatile`로 선언되어 있습니다.

```java
private volatile String nickname = "anonymous";
private volatile boolean registered;
```

여러 스레드가 값을 볼 가능성이 있는 상태에서 최신 값을 읽도록 의도를 드러냅니다.

## 30초 면접 답변

Thread는 Java 프로그램 안에서 독립적으로 실행되는 작업 흐름입니다. 서버에서는 여러 클라이언트나 요청을 동시에 처리하기 위해 스레드가 필요합니다. 제 `multichat-java` 프로젝트에서는 클라이언트가 접속할 때마다 `ClientSession` 작업을 만들고 `ExecutorService`에 제출해 Thread Pool에서 실행했습니다. 또한 같은 소켓에 동시에 쓰는 문제를 막기 위해 `send()`에 `synchronized`를 사용했습니다.

## 꼬리질문 대비

### Thread Pool의 장점은 무엇인가요?

스레드를 직접 계속 생성하는 비용을 줄이고, 작업 제출과 실행 관리를 분리할 수 있습니다. 또한 스레드 수를 제한하는 구조로 확장하면 서버 리소스를 더 안정적으로 관리할 수 있습니다.

### synchronized를 남용하면 어떤 문제가 있나요?

동시에 실행될 수 있는 코드가 줄어 성능이 떨어질 수 있고, 락 순서가 복잡해지면 데드락 위험이 생길 수 있습니다. 꼭 보호해야 하는 공유 자원에만 좁게 사용하는 것이 좋습니다.
