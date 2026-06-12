# Layered Architecture

## 한 줄 정의

계층형 아키텍처는 코드를 역할별 계층으로 나누어 책임을 분리하는 설계 방식입니다.

## 왜 필요한가

모든 코드를 한 곳에 넣으면 수정과 테스트가 어려워집니다. 역할별로 나누면 변경 영향 범위를 줄이고 테스트하기 쉬워집니다.

## 대표 계층

```text
Controller  HTTP 요청/응답 처리
Service     비즈니스 로직 처리
Repository  데이터 저장소 접근
Domain      핵심 데이터와 규칙
DTO         요청/응답 데이터 전달
```

## 내 프로젝트와 연결

`backend-interview-tracker`는 전형적인 계층형 구조입니다.

```text
QuestionController
-> QuestionService
-> QuestionRepository
-> Question Entity
```

컨트롤러는 요청을 받고, 서비스는 질문 생성/수정/삭제 로직을 처리하고, 리포지토리는 DB 접근을 담당합니다.

## 30초 면접 답변

계층형 아키텍처는 Controller, Service, Repository처럼 역할별로 코드를 분리하는 방식입니다. 이렇게 나누면 HTTP 요청 처리, 비즈니스 로직, 데이터 접근 책임이 섞이지 않아 유지보수와 테스트가 쉬워집니다. 제 `backend-interview-tracker` 프로젝트에서도 질문 API를 Controller-Service-Repository 구조로 나누어 구현했습니다.

