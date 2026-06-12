# ORM and JPA

## 한 줄 정의

ORM은 객체와 데이터베이스 테이블을 매핑해주는 기술이고, JPA는 Java에서 ORM을 사용하기 위한 표준 인터페이스입니다.

## 왜 필요한가

SQL을 직접 많이 작성하지 않아도 Java 객체 중심으로 데이터를 저장하고 조회할 수 있기 때문입니다.

## 핵심 개념

- Entity: DB 테이블과 매핑되는 객체
- Repository: Entity를 저장하고 조회하는 계층
- Persistence Context: JPA가 Entity 상태를 관리하는 공간
- Transaction: DB 작업을 하나의 단위로 묶는 것

## 내 프로젝트와 연결

`backend-interview-tracker`의 `Question` 클래스는 `@Entity`로 선언되어 DB 테이블과 매핑됩니다.

`QuestionRepository`는 `JpaRepository<Question, Long>`을 상속해 기본 CRUD를 제공합니다.

`QuestionService`는 생성, 수정, 삭제 메서드에 `@Transactional`을 사용해 DB 변경 작업을 하나의 트랜잭션으로 관리합니다.

## 30초 면접 답변

ORM은 객체와 DB 테이블을 매핑해주는 기술이고, JPA는 Java에서 ORM을 사용하기 위한 표준입니다. 제 `backend-interview-tracker` 프로젝트에서는 `Question` 엔티티를 만들고, `JpaRepository`를 통해 기본 CRUD를 처리했습니다. 서비스 계층에는 `@Transactional`을 사용해 생성, 수정, 삭제 작업을 트랜잭션 단위로 관리했습니다.

