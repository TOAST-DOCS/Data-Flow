## Data & Analytics > DataFlow > 코덱 설정 가이드

## 개요

* 코덱은 데이터의 입출력 형식을 결정하는 구성 요소입니다.
* Source 노드에서는 데이터 인입 시, Sink 노드에서는 데이터 출력 시 코덱이 적용됩니다.
* 다양한 코덱 타입을 지원하며, 각 코덱마다 고유한 특성과 사용 예시가 있습니다.

## 노드별 지원 코덱

### Source 노드

| 노드명                            | 지원 코덱       |
|--------------------------------|-------------|
| (NHN Cloud) Log & Crash Search | json        |
| (NHN Cloud) CloudTrail         | json        |
| (NHN Cloud) Object Storage     | json, plain |
| (Amazon) S3                    | json, plain |

### Sink 노드

| 노드명                        | 지원 코덱     |
|----------------------------|-----------|
| (NHN Cloud) Object Storage | json, line |
| (Amazon) S3                | json, line |
| stdout                     | json, line |

## 지원 코덱 타입

### JSON 코덱

* JSON 형식의 데이터를 파싱하여 각 필드를 개별적으로 처리합니다.
* Source 및 Sink 노드 모두 JSON의 모든 필드가 그대로 유지되기 때문에 필터링이나 가공에 유리합니다.

#### 조건

* 입력 데이터

```json
{"name": "DataFlow","type": "pipeline","status": "running","level": "info"}

```
* charset → `UTF-8`

#### 처리 결과

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

#### 전달 & 출력 데이터

```json
{"name": "DataFlow","type": "pipeline","status": "running","level": "info"}

```

### plain 코덱

* 원본 데이터를 문자열 형태로 처리합니다.
* Source 노드에서 입력 데이터를 `message` 필드에 문자열로 저장합니다.
* 원본 형태를 그대로 보존하고자 할 때 사용합니다.

#### plain 코덱 예제 - Source 노드
##### 조건
* 입력 데이터
  ```json
  {"name": "DataFlow","type": "pipeline","status": "running","level": "info"}
  ```
* charset → `UTF-8`

##### 처리 결과
```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### 전달 데이터
```json
{"message":"{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"}
```

### line 코덱

* 각 행을 하나의 메시지로 처리합니다.
* Source 노드에서는 입력된 각 행을 `message` 필드에 문자열로 저장하고, Sink 노드에서는 데이터를 format에 따라 텍스트 행으로 출력합니다.
* 행 단위 처리가 필요할 때 사용합니다.
* line 코덱의 경우 delimiter를 통해 출력되는 메시지들의 구분자를 정의할 수 있습니다.
  * 기본 값은 줄바꿈(`\n`)입니다.

#### line 코덱 예제 - Source 노드

##### 조건
* 입력 데이터
  ```text
  {"name":"DataFlow","type":"pipeline","status":"running","level":"info"}
  ```
* charset → `UTF-8`

##### 처리 결과
```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### 전달 데이터
```json
{"message":"{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"}
```

#### line 코덱 예제 - Sink 노드

##### 조건
* 입력 메시지
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  }
  ```
* delimiter → `\n`
* format → `[%{level}] %{name} %{status}`
* charset → `UTF-8`

##### 처리 결과
```text
[info] DataFlow running
```

##### 출력 데이터
```text
[info] DataFlow running
```
