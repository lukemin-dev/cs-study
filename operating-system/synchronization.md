# Synchronization

## 한 줄 정의

동기화는 여러 스레드가 공유 자원에 동시에 접근할 때 데이터가 깨지지 않도록 접근 순서를 제어하는 것입니다.

## 왜 필요한가

여러 스레드가 같은 데이터를 동시에 읽고 쓰면 예상하지 못한 결과가 생길 수 있습니다.

예를 들어 접속자 목록을 한 스레드가 순회하는 동안 다른 스레드가 삭제하면 오류나 누락이 발생할 수 있습니다.

```text
Thread A: sessions 순회
Thread B: sessions.remove()
```

## Race Condition

Race Condition은 실행 순서에 따라 결과가 달라지는 상황입니다.

```text
Thread A가 먼저 실행되면 정상
Thread B가 먼저 실행되면 오류
```

동시성 문제는 재현이 어렵기 때문에 처음부터 공유 자원을 조심해서 다뤄야 합니다.

## synchronized

`synchronized`는 한 번에 하나의 스레드만 특정 코드에 들어오게 합니다.

```java
synchronized void send(Message message) throws IOException {
    MessageCodec.write(socket.getOutputStream(), message);
}
```

`multichat-java`에서 `send()`를 동기화한 이유는 같은 클라이언트 소켓에 여러 스레드가 동시에 바이트를 쓰면 메시지가 섞일 수 있기 때문입니다.

## ConcurrentHashMap

접속자 목록은 여러 스레드가 동시에 접근합니다.

```java
private final Set<ClientSession> sessions = ConcurrentHashMap.newKeySet();
```

일반 `HashSet`은 동시 접근에 안전하지 않습니다. 그래서 동시성에 안전한 자료구조를 사용했습니다.

## volatile

`volatile`은 여러 스레드가 같은 변수를 볼 때 최신 값이 보이도록 하는 키워드입니다.

```java
private volatile String nickname = "anonymous";
private volatile boolean registered;
```

단, `volatile`은 복합 연산을 원자적으로 만들어주지는 않습니다.

```text
count++ 같은 연산
-> 읽기 + 증가 + 쓰기
-> volatile만으로는 동시성 안전하지 않음
```

## 내 프로젝트와 연결

`multichat-java`에는 동기화 포인트가 세 가지 있습니다.

```text
1. sessions
   -> ConcurrentHashMap.newKeySet()

2. send()
   -> synchronized

3. nickname, registered
   -> volatile
```

이 선택들은 모두 "여러 클라이언트 세션이 동시에 실행된다"는 전제에서 나옵니다.

## 30초 면접 답변

동기화는 여러 스레드가 공유 자원에 동시에 접근할 때 데이터가 깨지지 않도록 접근 순서를 제어하는 것입니다. 제 `multichat-java` 프로젝트에서는 접속자 목록을 여러 세션 스레드가 동시에 접근하기 때문에 `ConcurrentHashMap.newKeySet()`을 사용했습니다. 또한 같은 소켓에 여러 메시지가 동시에 쓰이면 바이트가 섞일 수 있어 `send()` 메서드에 `synchronized`를 적용했습니다.

## 꼬리질문 대비

### synchronized와 ConcurrentHashMap은 같은 역할인가요?

아닙니다. `synchronized`는 특정 코드 영역에 한 번에 하나의 스레드만 들어오게 하는 방식입니다. `ConcurrentHashMap`은 Map 내부에서 동시 접근을 더 안전하고 효율적으로 처리하도록 설계된 자료구조입니다.

### volatile만 쓰면 동시성 문제가 해결되나요?

아닙니다. `volatile`은 가시성을 보장하지만, 여러 단계로 이루어진 연산의 원자성을 보장하지 않습니다. 복합 연산에는 synchronized, lock, atomic class 등을 고려해야 합니다.
