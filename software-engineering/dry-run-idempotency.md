# Dry-run and Idempotency

## 한 줄 정의

dry-run은 실제 변경 없이 실행 결과를 미리 보여주는 방식이고, idempotency는 같은 작업을 여러 번 실행해도 결과가 의도치 않게 달라지지 않는 성질입니다.

## 왜 필요한가

파일 이동, GitHub 이슈 생성, 배포처럼 실제 상태를 바꾸는 작업은 실수하면 되돌리기 어렵습니다. 그래서 먼저 미리보기로 확인하고, 같은 작업을 반복해도 중복이나 손상이 생기지 않게 설계해야 합니다.

## 내 프로젝트와 연결

`file-organizer-agent`는 기본값이 dry-run입니다.

```text
python -m src.main
-> 실제 이동 없음
-> 어떤 파일이 어디로 이동될지 출력
```

실제로 이동하려면 `--apply`를 붙입니다.

`2025-fall-planner`도 같은 패턴을 사용합니다.

```text
node scripts/create-weekly-issues.mjs --week W1
-> 미리보기

node scripts/create-weekly-issues.mjs --week W1 --apply
-> 실제 GitHub 이슈 생성
```

## 30초 면접 답변

dry-run은 실제 변경 없이 어떤 작업이 수행될지 미리 보여주는 방식입니다. 파일 이동이나 이슈 생성처럼 외부 상태를 바꾸는 작업에서 실수를 줄이는 데 중요합니다. 제 `file-organizer-agent`와 `2025-fall-planner` 모두 기본은 미리보기로 두고, 실제 반영은 `--apply`를 붙였을 때만 하도록 만들었습니다.

