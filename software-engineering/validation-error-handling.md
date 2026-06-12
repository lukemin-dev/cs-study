# Validation and Error Handling

## 한 줄 정의

검증은 잘못된 입력을 초기에 걸러내는 과정이고, 에러 처리는 문제가 발생했을 때 일관된 방식으로 응답하거나 복구하는 과정입니다.

## 왜 필요한가

외부에서 들어오는 입력은 항상 신뢰할 수 없습니다.

```text
빈 제목
존재하지 않는 enum 값
잘못된 날짜 형식
없는 id 조회
권한 없는 파일 접근
```

이런 상황을 제대로 처리하지 않으면 프로그램이 예측하기 어려운 방식으로 실패합니다.

## 내 프로젝트와 연결

### backend-interview-tracker

컨트롤러에서 DTO에 `@Valid`를 사용합니다.

```java
public ApiResponse<Long> createQuestion(@Valid @RequestBody QuestionCreateRequest request)
```

검증 실패는 `GlobalExceptionHandler`가 잡아 400 응답으로 바꿉니다.

```text
잘못된 요청
-> MethodArgumentNotValidException
-> GlobalExceptionHandler
-> ApiResponse.error(...)
```

없는 질문 id는 서비스에서 `ResourceNotFoundException`으로 처리합니다.

```text
없는 질문 조회
-> ResourceNotFoundException
-> 404 Not Found
```

### 2025-fall-planner

`validateSemester()`는 설정 파일 오류를 GitHub에 반영하기 전에 막습니다.

```text
없는 과목명
잘못된 날짜 형식
중복 week id
알 수 없는 task type
```

### file-organizer-agent

폴더가 없거나 권한이 없을 수 있습니다.

```text
target_dir.exists()
PermissionError
```

파일을 실제로 옮기기 전 dry-run으로 먼저 보여주는 것도 넓은 의미에서 안전장치입니다.

## 에러 응답 설계

API에서는 에러를 일관된 형식으로 주는 것이 좋습니다.

```json
{
  "success": false,
  "message": "해당 질문을 찾을 수 없습니다.",
  "data": null
}
```

`backend-interview-tracker`의 `ApiResponse<T>`가 이 역할을 합니다.

## 상태 코드 감각

| 상태 코드 | 의미 | 예시 |
| --- | --- | --- |
| 400 | 잘못된 요청 | validation 실패, enum 파싱 실패 |
| 404 | 리소스 없음 | 없는 질문 id 조회 |
| 500 | 서버 내부 오류 | 예상하지 못한 예외 |

## 30초 면접 답변

검증은 잘못된 입력을 초기에 걸러내는 과정이고, 에러 처리는 문제가 발생했을 때 일관된 응답을 주는 과정입니다. 제 `backend-interview-tracker`에서는 요청 DTO에 `@Valid`를 적용해 잘못된 입력을 막고, `GlobalExceptionHandler`에서 validation 실패, not found, 잘못된 enum 값 등을 공통 응답 형식으로 변환했습니다. 이렇게 하면 클라이언트가 오류를 예측 가능한 형태로 처리할 수 있습니다.

## 꼬리질문 대비

### 검증은 프론트엔드와 백엔드 중 어디서 해야 하나요?

둘 다 해야 합니다. 프론트엔드 검증은 사용자 경험을 좋게 만들고, 백엔드 검증은 시스템의 최종 방어선입니다. 프론트엔드는 우회될 수 있으므로 백엔드 검증은 반드시 필요합니다.

### 400과 404는 어떻게 구분하나요?

400은 요청 형식이나 값이 잘못된 경우이고, 404는 요청 형식은 맞지만 해당 리소스가 존재하지 않는 경우입니다.
