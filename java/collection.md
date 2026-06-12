# Java Collection

## 한 줄 정의

Java Collection은 여러 데이터를 저장하고 다루기 위한 표준 자료구조 인터페이스와 구현체 모음입니다.

## 왜 필요한가

프로그램에서는 데이터를 하나만 다루는 경우보다 여러 개를 모아서 저장, 검색, 순회, 삭제하는 경우가 많습니다.

Java에서는 대표적으로 아래 구조를 자주 사용합니다.

```text
List  순서가 있는 목록
Set   중복 없는 집합
Map   key-value 저장소
```

## 핵심 비교

| 구분 | 특징 | 대표 구현체 | 예시 |
| --- | --- | --- | --- |
| List | 순서 있음, 중복 허용 | ArrayList, LinkedList | 질문 목록 |
| Set | 중복 없음 | HashSet, TreeSet | 접속자 집합 |
| Map | key-value | HashMap, TreeMap, ConcurrentHashMap | id로 객체 찾기 |

## List

`List`는 순서가 중요할 때 사용합니다.

```java
List<String> names = new ArrayList<>();
```

`ArrayList`는 배열 기반이라 인덱스 접근이 빠릅니다. 중간 삽입/삭제가 많으면 비용이 커질 수 있습니다.

## Set

`Set`은 중복이 없어야 할 때 사용합니다.

```java
Set<String> users = new HashSet<>();
```

`HashSet`은 hash 기반이라 평균적으로 추가, 삭제, 조회가 빠릅니다.

## Map

`Map`은 key로 value를 찾을 때 사용합니다.

```java
Map<Long, Question> questions = new HashMap<>();
```

DB에서 id로 질문을 찾는 것과 비슷하게, 메모리에서도 key를 기준으로 빠르게 값을 찾을 수 있습니다.

## 내 프로젝트와 연결

### multichat-java

접속자 목록은 `Set<ClientSession>`으로 관리합니다.

```java
private final Set<ClientSession> sessions = ConcurrentHashMap.newKeySet();
```

여기서 일반 `HashSet`이 아니라 `ConcurrentHashMap.newKeySet()`을 사용한 이유는 여러 클라이언트 세션 스레드가 동시에 접속자 목록에 접근하기 때문입니다.

```text
클라이언트 입장 -> sessions.add()
클라이언트 퇴장 -> sessions.remove()
메시지 전송 -> sessions 순회
```

### backend-interview-tracker

`Page<QuestionResponse>`는 질문 목록을 페이지 단위로 다룹니다. 모든 데이터를 한 번에 가져오지 않고 필요한 범위만 조회합니다.

### 2025-fall-planner

Node.js 코드지만 개념적으로는 배열과 Map을 많이 사용합니다.

```text
courses 배열
weeks 배열
courseByName Map
```

## 30초 면접 답변

Java Collection은 여러 데이터를 저장하고 다루기 위한 표준 자료구조입니다. 순서가 필요하면 List, 중복을 막고 싶으면 Set, key로 값을 찾고 싶으면 Map을 사용합니다. 제 `multichat-java` 프로젝트에서는 접속자 목록에 중복이 필요 없고 여러 스레드가 동시에 접근하기 때문에 `ConcurrentHashMap.newKeySet()` 기반 Set을 사용했습니다.

## 꼬리질문 대비

### ArrayList와 LinkedList 차이는 무엇인가요?

`ArrayList`는 배열 기반이라 인덱스 조회가 빠르지만 중간 삽입/삭제는 비용이 큽니다. `LinkedList`는 노드 연결 구조라 중간 삽입/삭제에 유리할 수 있지만, 특정 인덱스 접근은 순차 탐색이 필요합니다. 실무에서는 대부분 `ArrayList`를 먼저 고려합니다.

### HashMap과 ConcurrentHashMap 차이는 무엇인가요?

`HashMap`은 단일 스레드 환경에서 주로 사용하고, 여러 스레드가 동시에 접근하면 안전하지 않습니다. `ConcurrentHashMap`은 동시 접근을 고려한 Map으로 멀티스레드 환경에서 더 안전하게 사용할 수 있습니다.
