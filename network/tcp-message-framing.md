# TCP Message Framing

## 한 줄 정의

TCP 메시지 프레이밍은 TCP의 바이트 스트림에서 애플리케이션이 원하는 메시지 단위를 구분하는 방법입니다.

## 왜 필요한가

TCP는 데이터를 안정적으로 순서대로 전달하지만, "메시지 한 번 보내면 한 번 받는다"를 보장하지 않습니다.

예를 들어 클라이언트가 이렇게 보냈다고 해도:

```text
hello
world
```

서버는 이렇게 받을 수 있습니다.

```text
hell
oworld
```

또는 이렇게 받을 수도 있습니다.

```text
helloworld
```

TCP는 메시지 단위가 아니라 바이트 흐름 단위로 동작하기 때문입니다.

## 해결 방법

대표적인 프레이밍 방식은 세 가지입니다.

```text
1. delimiter 방식
   메시지 끝에 \n 같은 구분자를 붙입니다.

2. fixed-length 방식
   모든 메시지를 고정 길이로 맞춥니다.

3. length-prefix 방식
   메시지 앞에 payload 길이를 먼저 보냅니다.
```

`multichat-java`는 length-prefix 방식을 사용합니다.

```text
[payload 길이 4바이트][payload 내용]
```

## length-prefix 방식

보낼 때:

```text
Message 객체
-> payload bytes 생성
-> payload 길이를 4바이트 header로 작성
-> header + payload 전송
```

읽을 때:

```text
먼저 4바이트 읽기
-> payload 길이 계산
-> 그 길이만큼 정확히 읽기
-> Message 객체로 복원
```

## 내 프로젝트와 연결

`MessageCodec.write()`는 payload 길이를 먼저 씁니다.

```java
output.write(ByteBuffer.allocate(Integer.BYTES).putInt(payload.length).array());
output.write(payload);
output.flush();
```

`MessageCodec.read()`는 먼저 4바이트 header를 읽고, 그 길이만큼 payload를 읽습니다.

```java
byte[] header = readExactlyOrNull(input, Integer.BYTES);
int length = ByteBuffer.wrap(header).getInt();
byte[] payload = readExactly(input, length);
```

핵심은 `readExactly()`입니다. 한 번의 `input.read()`로 원하는 길이가 다 들어온다고 가정하지 않고, 목표 길이를 채울 때까지 반복해서 읽습니다.

## MAX_FRAME_SIZE

`MessageCodec`에는 최대 메시지 크기 제한이 있습니다.

```java
public static final int MAX_FRAME_SIZE = 64 * 1024;
```

이 제한이 없으면 잘못된 길이 값이나 악의적인 입력 때문에 서버가 너무 큰 메모리를 할당하려고 할 수 있습니다.

## EOF 처리

상대방이 연결을 정상 종료하면 stream에서 `-1`이 나올 수 있습니다.

`readExactlyOrNull()`은 header를 읽기 전 아무 바이트도 못 읽고 EOF를 만나면 `null`을 반환합니다. 이는 "상대가 연결을 닫았다"는 의미로 처리할 수 있습니다.

반대로 header나 payload를 읽는 중간에 EOF가 나면 프레임이 깨진 것이므로 예외를 던집니다.

## 30초 면접 답변

TCP는 데이터를 순서대로 안정적으로 전달하지만 메시지 경계는 보장하지 않는 스트림 기반 프로토콜입니다. 그래서 애플리케이션이 직접 메시지 단위를 구분해야 합니다. 제 `multichat-java` 프로젝트에서는 length-prefix 방식을 사용해 각 메시지 앞에 payload 길이 4바이트를 먼저 보내고, 수신 쪽에서는 그 길이만큼 정확히 읽어서 메시지를 복원했습니다. 또한 최대 프레임 크기를 제한해 비정상적으로 큰 메시지를 막았습니다.

## 꼬리질문 대비

### delimiter 방식보다 length-prefix를 쓴 이유는 무엇인가요?

delimiter 방식은 구현이 단순하지만 메시지 본문에 구분자가 들어올 때 escaping을 고민해야 합니다. length-prefix 방식은 먼저 길이를 알고 읽기 때문에 본문에 줄바꿈이나 특수문자가 있어도 안전하게 처리하기 쉽습니다.

### read를 한 번만 호출하면 안 되나요?

안 됩니다. TCP에서는 한 번의 read가 전체 메시지를 돌려준다는 보장이 없습니다. 원하는 길이를 다 받을 때까지 반복해서 읽어야 합니다.
