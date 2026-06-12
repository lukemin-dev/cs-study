# DNS

## 한 줄 정의

DNS는 사람이 읽기 쉬운 도메인 이름을 서버의 IP 주소로 바꿔주는 시스템입니다.

## 왜 필요한가

사용자가 `gyumin-archive.vercel.app` 같은 주소를 입력했을 때, 컴퓨터는 실제로 접속할 IP 주소를 알아야 합니다.

```text
gyumin-archive.vercel.app
-> DNS 조회
-> IP 주소
-> 서버 접속
```

## 내 프로젝트와 연결

`gyumin-archive`는 Vercel에 배포되어 있습니다. 사용자가 `https://gyumin-archive.vercel.app`에 접속하면 브라우저는 먼저 DNS를 통해 해당 도메인이 어느 서버를 가리키는지 확인합니다.

## 30초 면접 답변

DNS는 도메인 이름을 IP 주소로 변환해주는 시스템입니다. 사용자는 도메인을 입력하지만, 실제 네트워크 통신은 IP 주소를 기준으로 이루어지기 때문에 DNS 조회가 필요합니다. 예를 들어 제 포트폴리오 주소에 접속하면 먼저 DNS를 통해 Vercel 서버의 주소를 찾고, 그 다음 HTTP 요청을 보냅니다.

