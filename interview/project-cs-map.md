# Project CS Map

이 문서는 GitHub 프로젝트별로 연결해서 말할 수 있는 CS 지식을 정리한 맵입니다. 목표는 면접에서 "이 개념을 공부했습니다"가 아니라 "제가 만든 코드에서는 이 개념이 이렇게 쓰였습니다"라고 말할 수 있게 만드는 것입니다.

## 1. multichat-java

### 프로젝트 요약

Java Socket API와 Thread Pool을 사용한 터미널 기반 멀티 채팅 서버/클라이언트입니다.

### 연결되는 CS 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| TCP | `Socket`, `ServerSocket` | 채팅은 메시지 신뢰성이 중요해서 TCP를 사용했다. |
| 메시지 프레이밍 | `MessageCodec` | TCP는 스트림 기반이라 length-prefix로 메시지 경계를 직접 구분했다. |
| 프로세스/스레드 | `ExecutorService`, `ClientSession` | 여러 클라이언트를 동시에 처리하기 위해 세션별 작업을 Thread Pool에서 실행했다. |
| 동시성 자료구조 | `ConcurrentHashMap.newKeySet()` | 접속자 목록은 여러 스레드가 접근하므로 thread-safe한 Set을 사용했다. |
| 시간복잡도 | `broadcast()` | 접속자 n명에게 전송하므로 브로드캐스트는 O(n)이다. |
| 객체지향 | `ChatServer`, `ClientSession`, `MessageCodec` | 서버 관리, 세션 처리, 프로토콜 변환 책임을 분리했다. |

### 대표 면접 질문

- TCP와 UDP 중 왜 TCP를 선택했나요?
- TCP가 메시지 경계를 보장하지 않는다는 말은 무슨 뜻인가요?
- 클라이언트마다 스레드를 쓰면 어떤 장단점이 있나요?
- 접속자 목록에 일반 `HashSet`을 쓰면 어떤 문제가 생길 수 있나요?

## 2. backend-interview-tracker

### 프로젝트 요약

Spring Boot 기반 백엔드 면접 질문 관리 REST API입니다. 질문 CRUD, 검색/필터/정렬, JPA, H2, 예외 처리, 테스트가 포함되어 있습니다.

### 연결되는 CS/백엔드 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| HTTP/REST | `QuestionController` | URL, HTTP Method로 질문 리소스의 CRUD를 표현했다. |
| 계층형 구조 | Controller-Service-Repository | 요청 처리, 비즈니스 로직, 데이터 접근 책임을 분리했다. |
| ORM/JPA | `Question`, `QuestionRepository` | Java 객체와 DB 테이블을 매핑하고 Repository로 CRUD를 처리했다. |
| 트랜잭션 | `@Transactional` | 생성/수정/삭제 작업을 하나의 작업 단위로 관리했다. |
| 페이징/정렬 | `Pageable`, `PageRequest` | 목록 조회에서 데이터가 많아질 것을 고려해 page, size, sort를 지원했다. |
| 입력 검증 | `@Valid` DTO | 잘못된 요청이 서비스 로직까지 들어가지 않게 컨트롤러에서 검증했다. |
| 예외 처리 | `GlobalExceptionHandler` | 오류 응답 형식을 통일하고 HTTP 상태 코드를 구분했다. |
| 테스트 | MockMvc, JUnit, Mockito | API와 서비스 로직을 자동 검증했다. |

### 대표 면접 질문

- REST API를 설계할 때 URL과 HTTP Method를 어떻게 나눴나요?
- Controller, Service, Repository를 왜 분리하나요?
- JPA를 쓰면 어떤 장점과 주의점이 있나요?
- `@Transactional(readOnly = true)`를 왜 사용하나요?
- 공통 예외 처리기를 만든 이유는 무엇인가요?

## 3. backend-interview-question

### 프로젝트 요약

백엔드 면접 질문과 답변이 모여 있는 지식 저장소입니다. 직접 만든 애플리케이션이라기보다는 CS 면접 학습 자료에 가깝습니다.

### 연결되는 CS 지식

| CS 주제 | 코드/문서에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| 네트워크 | TCP/UDP, HTTP, DNS, OSI | 질문을 외우는 것이 아니라 내 프로젝트와 연결해 답변을 만들 자료로 쓴다. |
| 운영체제 | 프로세스/스레드, 동기/비동기 | `multichat-java`의 Thread Pool 설명과 연결할 수 있다. |
| 데이터베이스 | 트랜잭션, 인덱스, 정규화 | `backend-interview-tracker`의 JPA/DB 설계와 연결할 수 있다. |
| 면접 전략 | README 학습 팁 | 답변을 짧게 말하고 꼬리질문을 대비하는 방식으로 활용한다. |

### 대표 면접 질문

- 이 저장소를 그냥 외운 것이 아니라 어떻게 내 프로젝트 답변으로 바꿨나요?
- CS 질문을 공부할 때 어떤 기준으로 우선순위를 잡았나요?

## 4. seo-automation-system

### 프로젝트 요약

Google Search Console 형태의 페이지 성과 데이터를 분석해 SEO 개선 리포트를 만드는 Python 자동화 도구입니다.

### 연결되는 CS/개발 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| 파일 I/O | `load_rows()`, `write_text()` | JSON 입력을 읽고 Markdown 리포트를 파일로 생성했다. |
| 데이터 파이프라인 | `load_rows -> build_report` | 입력, 분류, 액션 생성, 리포트 출력으로 단계를 나눴다. |
| 조건 분기/분류 | `grade_page()` | clicks, impressions, CTR, position 기준으로 페이지를 분류했다. |
| 자동화 | `main.py`, CI | 수동 분석을 재현 가능한 명령어와 샘플 모드로 바꿨다. |
| 환경 변수/보안 | `.env.example` | API 키와 실데이터는 저장소에 올리지 않고 외부 설정으로 분리했다. |
| 재현성 | sample data | private credential 없이도 리뷰어가 핵심 로직을 검증할 수 있게 했다. |

### 대표 면접 질문

- 수동 SEO 분석을 어떤 기준으로 자동화했나요?
- 왜 sample mode를 만들었나요?
- API 키나 고객 데이터는 어떻게 보호해야 하나요?
- 분류 기준이 바뀌면 코드 구조를 어떻게 개선할 수 있나요?

## 5. file-organizer-agent

### 프로젝트 요약

파일 확장자를 기준으로 다운로드 폴더의 파일을 카테고리별 폴더로 정리하는 Python CLI 도구입니다.

### 연결되는 CS/개발 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| 파일시스템 | `Path`, `iterdir()`, `shutil.move()` | 폴더를 순회하고 파일 이동을 수행했다. |
| dry-run | `dry_run=True` | 실제 파일 이동 전에 계획을 먼저 보여줘 위험을 줄였다. |
| 충돌 처리 | `get_unique_name()` | 같은 이름의 파일이 있으면 `_1`, `_2` 접미사를 붙였다. |
| 자료구조 | `CATEGORIES` dict | 확장자와 카테고리 매핑을 설정 데이터로 분리했다. |
| 예외 처리 | `PermissionError` | 권한이 없는 폴더 접근 실패를 로그로 남겼다. |
| 로깅 | `setup_logging()` | 콘솔과 파일에 작업 흐름을 남겼다. |
| 테스트 | pytest | 분류, 중복 이름, 통합 흐름을 테스트로 검증했다. |

### 대표 면접 질문

- 실제 파일을 옮기는 도구에서 dry-run이 왜 중요한가요?
- 파일명이 중복되면 어떻게 처리했나요?
- 설정을 코드와 분리하면 어떤 장점이 있나요?
- 파일 이동 중 실패하면 어떤 문제가 생길 수 있나요?

## 6. 2025-fall-planner

### 프로젝트 요약

학기 과제 정보를 설정 파일에 적고, GitHub 라벨/마일스톤/이슈로 자동 생성하는 도구입니다.

### 연결되는 CS/개발 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| CLI 자동화 | Node scripts | 반복 작업을 명령어로 자동화했다. |
| 설정 파일 | `config/semester.json` | 코드 수정 없이 과목/과제 데이터를 바꿀 수 있게 했다. |
| 검증 | `validateSemester()` | 잘못된 설정이 GitHub에 반영되기 전에 차단했다. |
| dry-run/apply | `--apply` | 미리보기와 실제 반영을 분리해 실수를 줄였다. |
| API 연동 | `gh issue create`, `gh api` | GitHub CLI를 통해 GitHub API를 사용했다. |
| CI/워크플로 | `weekly-plan.yml` | 로컬 스크립트를 GitHub Actions에서도 실행할 수 있게 했다. |

### 대표 면접 질문

- 왜 설정 파일 기반으로 만들었나요?
- `--apply`를 둔 이유는 무엇인가요?
- GitHub Actions와 로컬 스크립트는 어떤 관계인가요?

## 7. gyumin-archive

### 프로젝트 요약

Next.js 기반 개인 포트폴리오 사이트입니다. `src/data`의 코드 데이터와 `content`의 Markdown 데이터를 함께 사용합니다.

### 연결되는 CS/웹 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| HTTP/DNS | Vercel 배포 사이트 | 브라우저가 도메인을 DNS로 해석하고 HTTP로 페이지를 요청한다. |
| 라우팅 | `src/app` | 폴더 구조가 URL 구조가 되는 App Router를 사용했다. |
| 정적 콘텐츠 | `content/**/*.md` | Markdown 파일을 읽어 프로젝트/노트 페이지로 변환했다. |
| 타입 시스템 | `src/types` | 포트폴리오 데이터의 필드 구조를 TypeScript 타입으로 관리했다. |
| 컴포넌트 분리 | `src/components` | 카드, 헤더, 섹션 등 반복 UI를 재사용했다. |
| 빌드/배포 | Vercel, CI | GitHub에 push하면 Vercel이 배포하도록 구성했다. |

### 대표 면접 질문

- Next.js App Router에서 폴더와 URL은 어떤 관계인가요?
- `content`와 `src/data`를 나눈 이유는 무엇인가요?
- Markdown 파일은 어떻게 화면 데이터가 되나요?

## 8. codetree

### 프로젝트 요약

CodeTree 문제 풀이를 날짜별로 저장한 알고리즘/기초 문법 연습 저장소입니다.

### 연결되는 CS 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| 변수/자료형 | Python/C++ 기초 문제 | 언어 기본 문법을 반복 연습했다. |
| 연산자 | 산술 연산, 나눗셈, 나머지 | 문제 풀이에서 정확한 연산 의미를 익혔다. |
| 입출력 | print, cout | 언어별 표준 출력 방식의 차이를 익혔다. |
| 시간복잡도 | 향후 문제 풀이 | 풀이마다 입력 크기와 반복문 비용을 정리하면 좋다. |
| 언어 비교 | Python/C++ 풀이 | 같은 문제를 언어별로 풀며 타입과 입출력 차이를 비교할 수 있다. |

### 대표 면접 질문

- 알고리즘 문제를 풀 때 시간복잡도는 어떻게 확인하나요?
- Python과 C++에서 정수 나눗셈/출력 방식은 어떻게 다른가요?
- 단순 풀이 저장소를 포트폴리오에서 어떻게 설명할 수 있나요?

## 9. 2-

### 프로젝트 요약

초기 CodeTree 풀이가 일부 들어 있는 작은 저장소입니다. 현재는 프로젝트보다는 학습 기록에 가깝습니다.

### 연결되는 CS 지식

| CS 주제 | 코드에서 보이는 지점 | 면접에서 말할 포인트 |
| --- | --- | --- |
| 입출력 | `print-two-lines.py` | 가장 기본적인 출력 문법을 연습했다. |
| 학습 기록 관리 | 날짜별 폴더 | 문제 풀이를 날짜별로 쌓기 시작한 흔적이다. |
| 저장소 정리 | README 부족 | `codetree`와 합치거나 archive 처리하는 것이 좋아 보인다. |

### 대표 면접 질문

- 이 저장소는 포트폴리오에서 강조할 프로젝트인가요?
- 학습 기록 저장소와 프로젝트 저장소는 어떻게 구분하나요?

## 우선순위

면접에서 강조할 우선순위는 아래처럼 잡는 것이 좋습니다.

```text
1. backend-interview-tracker
   REST API, Spring Boot, JPA, Transaction, Exception Handling, Test

2. multichat-java
   TCP, Socket, Thread, Protocol, Concurrent Collection

3. seo-automation-system
   Python Automation, Data Pipeline, File I/O, API Credential Handling

4. file-organizer-agent
   File System, Dry-run, Logging, Collision Handling, Test

5. gyumin-archive
   Next.js, Routing, Content Pipeline, Deployment

6. 2025-fall-planner
   GitHub Automation, Config Validation, Actions

7. codetree / 2-
   Algorithm Practice, Language Fundamentals
```

## 다음에 보강할 CS 문서

이 프로젝트 맵을 기준으로 다음 문서를 추가하면 좋습니다.

```text
network/rest-api.md
database/orm-jpa.md
operating-system/filesystem.md
software-engineering/layered-architecture.md
software-engineering/dry-run-idempotency.md
software-engineering/validation-error-handling.md
```

