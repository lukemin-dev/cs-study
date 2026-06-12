# HTTP

## 한 줄 정의

HTTP는 클라이언트와 서버가 요청과 응답을 주고받기 위한 애플리케이션 계층 프로토콜입니다.

## 왜 필요한가

웹 브라우저, 서버, API가 서로 약속된 방식으로 데이터를 주고받아야 하기 때문입니다.

```text
브라우저 -> 서버에 요청
서버 -> 브라우저에 응답
```

## 핵심 개념

- Request: 클라이언트가 서버에 보내는 요청
- Response: 서버가 클라이언트에 돌려주는 응답
- Method: GET, POST, PUT, PATCH, DELETE
- Status Code: 200, 201, 400, 401, 404, 500
- Header: 요청/응답에 대한 부가 정보
- Body: 실제 데이터

## 내 프로젝트와 연결

`gyumin-archive`는 Next.js 사이트입니다. 사용자가 브라우저에서 페이지에 접속하면 브라우저는 HTTP 요청을 보내고, Vercel/Next.js는 HTML, CSS, JavaScript, 이미지 등을 응답합니다.

`2025-fall-planner`는 GitHub CLI를 통해 GitHub API를 호출합니다. GitHub API도 내부적으로 HTTP 요청/응답 방식으로 동작합니다.

## 30초 면접 답변

HTTP는 클라이언트와 서버가 요청과 응답을 주고받기 위한 프로토콜입니다. 클라이언트는 GET, POST 같은 메서드로 요청을 보내고, 서버는 상태 코드와 데이터를 응답합니다. 제 포트폴리오 사이트도 브라우저가 HTTP 요청을 보내면 Next.js/Vercel이 페이지와 정적 파일을 응답하는 방식으로 동작합니다.

