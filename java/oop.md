# Object-Oriented Programming

## 한 줄 정의

객체지향 프로그래밍은 데이터와 동작을 객체 단위로 묶어서 프로그램을 구성하는 방식입니다.

## 왜 필요한가

복잡한 프로그램을 역할별로 나누어 이해하고 수정하기 쉽게 만들기 위해 필요합니다.

## 핵심 개념

- Encapsulation: 데이터와 동작을 하나로 묶고 필요한 부분만 공개합니다.
- Abstraction: 복잡한 내부 구현보다 핵심 역할만 드러냅니다.
- Inheritance: 공통 기능을 물려받아 재사용합니다.
- Polymorphism: 같은 인터페이스로 다양한 구현을 다룰 수 있습니다.

## 내 프로젝트와 연결

`multichat-java`는 역할별로 클래스를 나누었습니다.

```text
ChatServer      서버 전체 관리
ClientSession   클라이언트 한 명 처리
Message         메시지 데이터
MessageCodec    메시지 인코딩/디코딩
Log             로그 출력
```

이렇게 나누면 `MessageCodec`만 따로 테스트하거나, `ClientSession`의 명령어 처리만 따로 개선하기 쉬워집니다.

## 30초 면접 답변

객체지향은 데이터와 동작을 객체 단위로 묶어 프로그램을 구성하는 방식입니다. 역할별로 클래스를 나누면 코드의 책임이 명확해지고 유지보수가 쉬워집니다. 제 채팅 프로젝트에서도 서버 관리, 클라이언트 세션 처리, 메시지 변환 역할을 각각 `ChatServer`, `ClientSession`, `MessageCodec`으로 분리했습니다.

