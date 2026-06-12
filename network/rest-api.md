# REST API

## 한 줄 정의

REST API는 HTTP Method와 URL을 이용해 서버의 자원을 다루는 API 설계 방식입니다.

## 왜 필요한가

클라이언트와 서버가 어떤 자원을 어떤 방식으로 처리할지 일관되게 약속하기 위해 필요합니다.

```text
GET    /api/questions       질문 목록 조회
GET    /api/questions/1     질문 단건 조회
POST   /api/questions       질문 생성
PUT    /api/questions/1     질문 수정
DELETE /api/questions/1     질문 삭제
```

## 내 프로젝트와 연결

`backend-interview-tracker`는 질문을 하나의 리소스로 보고 REST API를 구성했습니다.

`QuestionController`는 HTTP 요청을 받고, 실제 로직은 `QuestionService`에 맡깁니다.

## 30초 면접 답변

REST API는 HTTP Method와 URL로 자원을 표현하는 API 설계 방식입니다. 예를 들어 질문을 관리하는 API라면 `GET /api/questions`는 목록 조회, `POST /api/questions`는 생성, `DELETE /api/questions/{id}`는 삭제처럼 표현할 수 있습니다. 제 `backend-interview-tracker` 프로젝트도 질문 리소스를 기준으로 CRUD API를 설계했습니다.

