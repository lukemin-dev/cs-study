# Index

## 한 줄 정의

인덱스는 데이터베이스에서 원하는 데이터를 더 빠르게 찾기 위해 특정 컬럼 기준으로 만들어두는 검색용 자료구조입니다.

## 왜 필요한가

테이블에 데이터가 많아지면 조건에 맞는 행을 찾기 위해 전체 테이블을 처음부터 끝까지 확인하는 비용이 커집니다.

```text
인덱스 없음
-> 전체 행을 순서대로 확인
-> Full Table Scan

인덱스 있음
-> 정렬된 자료구조에서 후보를 빠르게 찾음
-> Index Scan
```

## 핵심 개념

- 인덱스는 조회 성능을 높일 수 있습니다.
- 인덱스도 별도 저장 공간을 사용합니다.
- INSERT, UPDATE, DELETE 때 인덱스도 같이 갱신해야 하므로 쓰기 성능은 떨어질 수 있습니다.
- 모든 컬럼에 인덱스를 거는 것이 좋은 것은 아닙니다.
- 자주 검색, 정렬, 조인에 쓰이는 컬럼이 후보가 됩니다.

## B-Tree 감각

일반적인 DB 인덱스는 B-Tree 계열 자료구조를 많이 사용합니다.

```text
정렬된 구조
-> 비교 횟수를 줄임
-> 범위 검색에도 유리
```

예를 들어 `createdAt desc` 정렬을 자주 한다면 `createdAt` 인덱스를 고려할 수 있습니다.

## 내 프로젝트와 연결

`backend-interview-tracker`의 질문 목록 조회는 아래 조건을 지원합니다.

```text
keyword
category
status
page
size
sort
```

현재는 H2 In-memory DB와 JPQL 검색을 사용합니다.

```java
@Query("SELECT q FROM Question q WHERE " +
       "(:keyword IS NULL OR q.title LIKE %:keyword% OR q.answer LIKE %:keyword%) " +
       "AND (:category IS NULL OR q.category = :category) " +
       "AND (:status IS NULL OR q.status = :status)")
Page<Question> searchQuestions(...)
```

데이터가 적을 때는 큰 문제가 없지만, 질문이 많아지면 아래 컬럼에 인덱스를 고려할 수 있습니다.

```text
category
status
createdAt
updatedAt
```

다만 `LIKE %keyword%` 검색은 일반 B-Tree 인덱스를 잘 활용하기 어렵습니다. 제목이나 답변 전문 검색을 제대로 하려면 DB의 full-text search나 별도 검색 엔진을 검토할 수 있습니다.

## 인덱스 후보 판단 기준

좋은 후보:

- WHERE 조건에 자주 사용되는 컬럼
- ORDER BY에 자주 사용되는 컬럼
- JOIN 조건에 자주 사용되는 컬럼
- 값의 종류가 적당히 다양해서 필터링 효과가 있는 컬럼

주의할 후보:

- 값 종류가 너무 적은 컬럼
- 자주 수정되는 컬럼
- 거의 조회하지 않는 컬럼
- 작은 테이블의 컬럼

## 30초 면접 답변

인덱스는 데이터베이스에서 원하는 데이터를 빠르게 찾기 위해 특정 컬럼 기준으로 만들어두는 검색용 자료구조입니다. 조회 성능을 높일 수 있지만, 저장 공간이 필요하고 데이터 변경 시 인덱스도 갱신해야 하므로 쓰기 성능에는 비용이 생깁니다. 제 `backend-interview-tracker` 프로젝트에서는 질문 목록을 `category`, `status`, `createdAt` 기준으로 조회하거나 정렬할 수 있어서 데이터가 많아진다면 이런 컬럼에 인덱스를 고려할 수 있습니다.

## 꼬리질문 대비

### 인덱스는 많을수록 좋은가요?

아닙니다. 인덱스는 조회에는 도움이 되지만 쓰기 작업마다 함께 갱신되어야 합니다. 그래서 조회 패턴을 보고 필요한 컬럼에만 적용해야 합니다.

### `LIKE %keyword%`는 인덱스를 잘 타나요?

일반적인 B-Tree 인덱스는 앞부분부터 정렬된 구조라서 앞에 `%`가 붙은 패턴은 인덱스를 활용하기 어렵습니다. 전문 검색이 필요하면 full-text index나 검색 엔진을 고려할 수 있습니다.
