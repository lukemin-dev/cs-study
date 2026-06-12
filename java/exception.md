# Java Exception

## 한 줄 정의

예외는 프로그램 실행 중 정상 흐름으로 처리할 수 없는 문제가 발생했음을 표현하는 객체입니다.

## 왜 필요한가

파일을 읽을 수 없거나, 네트워크 연결이 끊기거나, 사용자가 잘못된 값을 보내는 상황은 언제든 발생할 수 있습니다. 이런 상황을 무시하면 프로그램이 비정상 종료되거나 잘못된 결과를 만들 수 있습니다.

## Checked Exception과 Unchecked Exception

| 구분 | 의미 | 예시 |
| --- | --- | --- |
| Checked Exception | 컴파일 시점에 처리 강제 | IOException |
| Unchecked Exception | 실행 중 발생, 처리 강제 없음 | NullPointerException, IllegalArgumentException |

## 내 프로젝트와 연결

### multichat-java

소켓 통신은 언제든 끊길 수 있습니다.

```java
catch (IOException exception) {
    Log.error("SESSION", "connection lost for " + nickname, exception);
}
```

네트워크 프로그램에서는 연결 실패, 중간 종료, broken pipe 같은 상황을 정상적인 가능성으로 보고 처리해야 합니다.

### backend-interview-tracker

없는 질문 id를 조회하면 `ResourceNotFoundException`을 던집니다.

```java
return questionRepository.findById(id)
        .orElseThrow(() -> new ResourceNotFoundException(...));
```

그리고 `GlobalExceptionHandler`가 이를 받아 HTTP 404 응답으로 바꿉니다.

```text
Service에서 예외 발생
-> GlobalExceptionHandler에서 처리
-> ApiResponse.error(...) 반환
```

### file-organizer-agent

Python 프로젝트지만 예외 처리 개념은 같습니다. 폴더 접근 권한이 없으면 `PermissionError`를 잡고 로그를 남깁니다.

## 좋은 예외 처리 기준

- 예외를 무시하지 않습니다.
- 사용자에게 필요한 메시지를 제공합니다.
- 내부 구현 정보는 과하게 노출하지 않습니다.
- 로그에는 원인을 추적할 수 있는 정보를 남깁니다.
- HTTP API라면 적절한 상태 코드로 바꿉니다.

## 30초 면접 답변

예외는 프로그램 실행 중 정상 흐름으로 처리하기 어려운 문제가 발생했음을 표현하는 객체입니다. Java에서는 `IOException` 같은 checked exception과 `IllegalArgumentException` 같은 unchecked exception이 있습니다. 제 `backend-interview-tracker`에서는 없는 질문을 조회하면 `ResourceNotFoundException`을 던지고, `GlobalExceptionHandler`에서 이를 404 응답으로 변환해 API 오류 형식을 통일했습니다.

## 꼬리질문 대비

### 예외를 Service에서 처리할지 Controller에서 처리할지 어떻게 나누나요?

Service는 비즈니스 상황을 판단해서 예외를 던지고, Controller 또는 `GlobalExceptionHandler`는 그 예외를 HTTP 응답으로 변환하는 역할을 맡기는 것이 깔끔합니다.

### 모든 예외를 `Exception`으로 잡으면 안 되나요?

가능은 하지만 좋지 않습니다. 예외 종류별로 원인과 대응 방법이 다르기 때문입니다. 예를 들어 validation 실패는 400, 없는 리소스는 404, 서버 내부 오류는 500으로 구분하는 것이 좋습니다.
