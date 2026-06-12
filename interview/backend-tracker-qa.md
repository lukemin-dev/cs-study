# Backend Interview Tracker Q&A

이 문서는 `backend-interview-tracker` 프로젝트를 면접에서 설명하기 위한 질문/답변 모음입니다.

## 1. 이 프로젝트를 한 문장으로 설명해보세요.

백엔드 면접 질문과 답변을 카테고리별로 저장하고, 검색/필터/정렬하면서 학습 상태를 관리할 수 있는 Spring Boot REST API 서버입니다.

## 2. 왜 Spring Boot로 만들었나요?

Spring Boot는 REST API 서버를 빠르게 만들 수 있고, validation, exception handling, JPA, test 같은 백엔드 기본기를 한 프로젝트 안에서 연습하기 좋다고 판단했습니다. 또한 실제 백엔드 채용에서 Spring Boot 경험을 많이 보기 때문에 면접 대비 프로젝트로 적합했습니다.

## 3. Controller, Service, Repository를 왜 나눴나요?

역할을 분리하기 위해서입니다.

```text
Controller  HTTP 요청과 응답 처리
Service     비즈니스 로직 처리
Repository  DB 접근 처리
```

이렇게 나누면 요청 처리 코드와 데이터 접근 코드가 섞이지 않고, 서비스 로직을 테스트하기도 쉬워집니다.

## 4. REST API 설계는 어떻게 했나요?

질문을 하나의 리소스로 보고 `/api/questions` 아래에 CRUD API를 구성했습니다.

```text
POST   /api/questions       질문 생성
GET    /api/questions/{id}  질문 단건 조회
GET    /api/questions       질문 목록/검색
PUT    /api/questions/{id}  질문 수정
DELETE /api/questions/{id}  질문 삭제
```

HTTP Method가 동작을 나타내고 URL은 자원을 나타내도록 구성했습니다.

## 5. JPA를 사용한 이유는 무엇인가요?

질문 데이터를 Java 객체 중심으로 다루고 싶었기 때문입니다. `Question`을 엔티티로 만들고 `QuestionRepository`가 `JpaRepository`를 상속하게 해서 기본 CRUD를 간단하게 처리했습니다. 또한 `@Transactional`을 통해 DB 변경 작업을 트랜잭션 단위로 관리할 수 있습니다.

## 6. `@Transactional`은 왜 사용했나요?

DB 변경 작업을 하나의 작업 단위로 묶기 위해 사용했습니다. 생성, 수정, 삭제 도중 문제가 생기면 일부만 반영되는 것을 막을 수 있습니다. 조회 메서드에는 `readOnly = true`를 사용해 읽기 전용 의도를 드러냈습니다.

## 7. 검색 API는 어떻게 구현했나요?

`GET /api/questions`에서 `keyword`, `category`, `status`, `page`, `size`, `sort` 파라미터를 받도록 했습니다. Repository에서는 JPQL로 조건이 있을 때만 필터링되도록 작성했습니다.

```text
keyword가 있으면 title/answer 검색
category가 있으면 category 필터
status가 있으면 status 필터
```

또한 `Pageable`을 사용해 한 번에 모든 데이터를 가져오지 않고 페이지 단위로 조회했습니다.

## 8. 예외 처리는 어떻게 했나요?

`GlobalExceptionHandler`를 만들어 예외 응답 형식을 통일했습니다.

```text
ResourceNotFoundException -> 404
MethodArgumentNotValidException -> 400
HttpMessageNotReadableException -> 400
예상하지 못한 Exception -> 500
```

응답은 `ApiResponse<T>`로 감싸서 성공/실패 형식을 일정하게 만들었습니다.

## 9. 입력 검증은 왜 필요한가요?

외부 요청은 항상 신뢰할 수 없기 때문입니다. 제목이나 답변이 비어 있거나, enum 값이 잘못 들어오거나, JSON 형식이 깨질 수 있습니다. DTO에 validation을 적용하면 잘못된 입력이 서비스 로직까지 들어가기 전에 차단할 수 있습니다.

## 10. H2 In-memory DB를 사용한 이유는 무엇인가요?

학습용 프로젝트에서 빠르게 실행하고 테스트하기 위해 사용했습니다. 별도 DB 설치 없이 API와 JPA 흐름을 검증할 수 있습니다. 다만 실제 운영 환경이라면 MySQL이나 PostgreSQL 같은 외부 DB를 사용하고, Docker Compose로 로컬 환경을 구성하는 방향이 좋습니다.

## 11. 테스트는 어떤 의미가 있나요?

테스트는 코드를 수정해도 기존 기능이 깨지지 않았는지 확인하는 안전장치입니다. Controller 테스트는 HTTP 요청/응답 흐름을 확인하고, Service 테스트는 질문 생성, 조회, 수정, 삭제 같은 비즈니스 로직을 검증합니다.

## 12. 이 프로젝트의 한계는 무엇인가요?

현재는 H2 In-memory DB를 사용하므로 서버를 재시작하면 데이터가 사라집니다. 인증/인가도 없어서 실제 서비스로 쓰기에는 부족합니다. 또한 검색은 JPQL 기반 단순 검색이라 데이터가 많아지면 인덱스나 전문 검색을 고민해야 합니다.

## 13. 다음에 개선한다면 무엇을 하겠나요?

우선 MySQL과 Docker Compose를 적용해 실제 운영에 가까운 DB 환경을 만들고 싶습니다. 그 다음 Spring Security와 JWT를 추가해 사용자별 질문 관리가 가능하게 만들 수 있습니다. 검색이 복잡해지면 QueryDSL이나 full-text search도 검토할 수 있습니다.

## 14. 면접에서 강조할 포인트

```text
1. REST API 설계 경험
2. Controller-Service-Repository 계층 분리
3. JPA Entity와 Repository 사용
4. @Transactional 기반 DB 작업 관리
5. GlobalExceptionHandler로 예외 응답 통일
6. Pageable 기반 검색/필터/정렬
7. 테스트 코드로 기능 검증
```

## 15. 1분 답변

`backend-interview-tracker`는 백엔드 면접 질문을 저장하고 검색할 수 있는 Spring Boot REST API 서버입니다. 질문을 리소스로 보고 CRUD API를 설계했고, Controller-Service-Repository 구조로 요청 처리, 비즈니스 로직, 데이터 접근 책임을 분리했습니다. JPA를 사용해 `Question` 엔티티를 DB와 매핑했고, 생성/수정/삭제에는 `@Transactional`을 적용했습니다. 또한 `GlobalExceptionHandler`와 `ApiResponse`로 오류 응답 형식을 통일했고, 검색/필터/정렬은 `Pageable`과 JPQL을 이용해 구현했습니다. 지금 다시 개선한다면 MySQL, Docker Compose, Spring Security/JWT를 추가해 더 실제 서비스에 가까운 구조로 확장하고 싶습니다.
