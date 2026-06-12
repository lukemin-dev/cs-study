# Hash

## 한 줄 정의

Hash는 데이터를 고정된 규칙으로 숫자 값으로 바꾸고, 그 값을 이용해 빠르게 저장하거나 찾는 방식입니다.

## 왜 필요한가

데이터를 빠르게 찾기 위해 필요합니다.

예를 들어 이름으로 사용자를 찾을 때 모든 사용자를 처음부터 끝까지 확인하면 느립니다. HashMap을 사용하면 평균적으로 훨씬 빠르게 찾을 수 있습니다.

## 핵심 개념

- Hash Function: 값을 해시 값으로 바꾸는 함수
- Hash Collision: 서로 다른 값이 같은 해시 위치를 갖는 상황
- HashMap: key-value 형태로 데이터를 저장하는 자료구조
- HashSet: 중복 없는 값을 저장하는 자료구조

## 내 프로젝트와 연결

`multichat-java`에서는 접속자 목록을 `Set<ClientSession>`으로 관리합니다.

```java
private final Set<ClientSession> sessions = ConcurrentHashMap.newKeySet();
```

여러 스레드가 동시에 접근할 수 있기 때문에 일반 `HashSet`이 아니라 동시성에 안전한 구조를 사용했습니다.

## 30초 면접 답변

Hash는 데이터를 해시 함수로 변환한 값을 이용해 빠르게 저장하고 조회하는 방식입니다. HashMap이나 HashSet은 평균적으로 빠른 조회 성능을 제공하지만, 충돌이 발생할 수 있습니다. 제 채팅 서버에서는 접속자 목록을 Set으로 관리했고, 여러 스레드가 동시에 접근하기 때문에 `ConcurrentHashMap.newKeySet()`을 사용했습니다.

