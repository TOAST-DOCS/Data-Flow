## Data & Analytics > DataFlow > 노드 설정 가이드

* 노드 유형은 손쉽게 플로우를 작성할 수 있게 사전 정의된 템플릿입니다.
* 노드 유형의 종류는 Source, Filter, Branch, Sink입니다.
* Source, Sink 노드 유형은 반드시 테스트를 수행하여 엔드포인트 정보가 유효한지 확인하기를 권장합니다.
* 접근 제어가 설정된 데이터 소스 연결 시에는 DataFlow IP 고정 기능을 사용해야 합니다.
    * DataFlow IP 고정 기능을 사용하려면 고객 센터로 문의하세요.

## Domain Specific Language(DSL) 정의

* 플로우 실행에 필요한 DSL 정의입니다.

### Variable

* `{{ executionTime }}`
    * 플로우 실행 시간
* 시간 단위 ( unit )
    * 분 - `{{ MINUTE }}`
    * 시 - `{{ HOUR }}`
    * 일 - `{{ DAY }}`
    * 월 - `{{ MONTH }}`
    * 년 - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 시작 시간을 리턴합니다.
    * [주의] 한국 시간을 기준으로 계산합니다.
    * ex\) \{\{ executionTime \| startOf: MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 마지막 시간을 리턴합니다.
    * [주의] 한국 시간을 기준으로 계산합니다.
    * ex\) \{\{ executionTime \| endOf: MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 `delta`만큼 뺀 시간을 리턴합니다.
    * ex\) \{\{ executionTime \| subTime: 10, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 `delta`만큼 더한 시간을 리턴합니다.
    * ex\) \{\{ executionTime \| addTime: 10, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10, MINUTE \}\}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * 주어진 시간을 `formatStr` 형태로 리턴합니다.
        * ios8601
        * yyyy
        * yy
        * MM
        * M
        * dd
        * d
        * mm
        * m
        * ss
        * s
    * ex\) \{\{ executionTime \| format: 'yyyy' \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| format: 'yyyy' \}\}
        * → 2022
* nested filter 예제
    * 플로우 실행이 시작된 날의 03시의 DSL 표현
        * → \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## 자료형별 입력 방법
### string
* 문자열을 입력합니다.

### number
* 0 이상의 숫자를 입력합니다.
* 입력창 오른쪽의 화살표를 이용해 값을 1씩 조절할 수 있습니다.

### boolean
* 드롭다운 메뉴에서 `TRUE` 혹은 `FALSE`를 선택합니다.

### enum
* 드롭다운 메뉴에서 항목을 선택합니다.

### array of strings
* 배열에 들어갈 문자열을 하나씩 입력합니다.
* 문자열 입력 후 `+` 버튼을 클릭하면 배열에 문자열이 삽입됩니다.
* ex) `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`를 입력하고자 하는 경우 `message`, `yyyy-MM-dd HH:mm:ssZ`, `ISO8601`의 순서로 배열에 문자열을 삽입합니다.

### hash
* json 형식의 문자열을 입력합니다.

## Source

* 플로우로 데이터를 인입할 엔드포인트를 정의하는 노드 유형입니다.

### 실행 모드

* Source 노드에는 실행 모드가 존재하며, BATCH 모드와 STREAMING 모드로 나뉩니다.
    * STREAMING 모드: 플로우를 종료하지 않고 실시간으로 데이터를 처리합니다.
    * BATCH 모드: 정해진 데이터를 처리한 후 플로우를 종료합니다.
* Source 노드 별로 지원하는 실행 모드가 다릅니다.
* 하나의 플로우에서는 모든 Source 노드가 동일한 실행 모드로 설정되어야 합니다.

### Source 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 유형 | - | string | 각 메시지에 주어진 값으로 `type` 필드를 생성합니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |
| 태그 | - | array of strings | 각 메시지에 주어진 값의 태그를 추가합니다. |  |
| 필드 추가 | - | hash | 커스텀 필드를 추가할 수 있습니다.<br/>`%{[depth1_field]}`로 각 필드의 값을 가져와 필드를 추가할 수 있습니다. |  |

### 필드 추가 예제

``` json
{
    "my_custom_field": "%{[json_body][logType]}"
}
```

## Source > (NHN Cloud) Log & Crash Search

### 노드 설명

* (NHN Cloud) Log & Crash Search 노드는 Log & Crash Search로부터 로그를 읽어오는 노드입니다.
* 노드에 로그 조회 시작 시간을 설정할 수 있습니다. 설정하지 않으면 플로우를 시작하는 시점부터 로그를 읽어 옵니다.
* 노드에 종료 시간을 입력하지 않으면 스트리밍 형식으로 로그를 읽어 옵니다. 종료 시간을 입력하면 종료 시간까지의 로그를 읽어 오고 플로우는 종료됩니다.
* ```현재 세션 로그와 크래시 로그는 지원하지 않습니다.```
* Log & Crash Search의 로그 검색 API의 토큰에 영향을 받습니다.
    * 토큰이 부족할 경우 Log & Crash Search로 문의하세요.

### 실행 모드
* STREAMING: `조회 시작 시간` 이후의 데이터를 계속해서 처리합니다.
* BATCH: `조회 시작 시간`, `조회 종료 시간` 사이에 해당하는 데이터를 모두 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Log & Crash Search의 앱키를 입력합니다. |  |
| SecretKey | - | string | Log & Crash Search의 시크릿키를 입력합니다. |  |
| 조회 시작 시간 | - | string | 로그 조회의 시작 시간을 입력합니다. | [참고](#dsl) |
| 조회 종료 시간 | - | string | 로그 조회의 종료 시간을 입력합니다. |  |
| 재시도 횟수 | - | number | 로그 조회가 실패했을 때 재시도할 최대 횟수를 입력합니다. |  |

* 재시도 횟수 설정
    * 재시도 횟수만큼 실패하면 더 이상 로그 조회를 시도하지 않고, 플로우는 종료됩니다.

### 코덱별 메시지 인입

* Log & Crash Search는 기본적으로 ```JSON``` 형식의 데이터를 다룹니다.
* 코덱을 선택하지 않거나 plain인 경우 Log & Crash Search 로그에 대한 JSON 문자열을 `message`라는 필드로 포함하게 됩니다.
* Log & Crash Search 로그의 각 필드를 활용하고 싶다면 json 코덱을 사용하는 것이 좋습니다.

#### 미선택 혹은 plain

``` js
{
    "message":"{\\\"log\\\":\\\"&\\\", \\\"Crash\\\": \\\"Search\\\", \\\"Result\\\": \\\"Data\\\"}"
}
```

#### json

``` js
{"log":"&", "Crash": "Search", "Result": "Data"}
```

## Source > (NHN Cloud) CloudTrail

### 노드 설명

* (NHN Cloud) CloudTrail은 CloudTrail로부터 데이터를 읽어 오는 노드입니다.
* 노드에 데이터 조회 시작 시간을 설정할 수 있습니다. 설정하지 않으면 플로우를 시작하는 시점부터 데이터를 읽어 옵니다.
* 노드에 종료 시간을 입력하지 않으면 스트리밍 형식으로 데이터를 읽어 옵니다. 종료 시간을 입력하면 종료 시간까지의 데이터를 읽어 오고 플로우는 종료됩니다.

### 실행 모드
* STREAMING: `조회 시작 시간` 이후의 데이터를 계속해서 처리합니다.
* BATCH: `조회 시작 시간`, `조회 종료 시간` 사이에 해당하는 데이터를 모두 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | CloudTrail의 앱키를 입력합니다. |  |
| 조회 시작 시간 | - | string | 데이터 조회의 시작 시간을 입력합니다. | [참고](#dsl) |
| 조회 종료 시간 | - | string | 데이터 조회의 종료 시간을 입력합니다. |  |
| 재시도 횟수 | - | number | 데이터 조회가 실패했을 때 재시도할 최대 횟수를 입력합니다. |  |

* 재시도 횟수 설정
    * 재시도 횟수만큼 실패하면 더 이상 데이터 조회를 시도하지 않고, 플로우는 종료됩니다.

### 코덱별 메시지 인입

* CloudTrail은 기본적으로 ```JSON``` 형식의 데이터를 다루고 있습니다.
* 코덱을 선택하지 않거나 plain인 경우 CloudTrail 데이터에 대한 JSON 문자열을 `message`라는 필드로 포함하게 됩니다.
* CloudTrail 데이터의 각 필드를 활용하고 싶다면 json 코덱을 사용하는 것이 좋습니다.

#### 미선택 혹은 plain

``` js
{
    "message":"{\\\"log\\\":\\\"CloudTrail\\\", \\\"Result\\\": \\\"Data\\\", \\\"@timestamp\\\": \\\"2023-12-06T08:09:24.887Z\\\", \\\"@version\\\": \\\"1\\\"}"
}
```

#### json

``` js
{"log":"CloudTrail", "Result": "Data", "@timestamp": "2023-12-06T08:09:24.887Z", "@version":"1"}
```

## Source > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage로부터 데이터를 입력 받는 노드입니다.
* 오브젝트 생성 시간을 기준으로 가장 빨리 생성된 오브젝트부터 데이터를 읽습니다.

### 실행 모드
* STREAMING: `리스트 갱신 주기`마다 오브젝트 리스트를 갱신하며, 새롭게 추가된 오브젝트들을 읽어 데이터를 처리합니다.
* BATCH: 플로우 시작 시점에 오브젝트 리스트를 한번 불러온 뒤, 오브젝트들을 읽어 데이터를 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 버킷 | - | string | 데이터를 읽을 버킷 이름을 입력합니다. |  |
| 리전 | - | string | 저장소에 설정된 리전 정보를 입력합니다. |  |
| 비밀 키 | - | string | S3가 발급한 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | - | string | S3가 발급한 자격 증명 액세스 키를 입력합니다. |  |
| 리스트 갱신 주기 | - | number | 버킷에 포함된 오브젝트 리스트 갱신 주기를 입력합니다. |  |
| 메타데이터 포함 여부 | - | boolean | S3 오브젝트의 메타데이터를 키로 포함할지 여부를 결정합니다. 메타데이터 필드를 Sink 플러그인에 노출하기 위해서는 filter 노드 유형을 조합해야 합니다(하단 가이드 참조). | 생성되는 필드는 다음과 같습니다.<br/>last_modified: 오브젝트가 마지막으로 수정된 시간<br/>content_length: 오브젝트 크기<br/>key: 오브젝트 이름<br/>content_type: 오브젝트 형식<br/>metadata: 메타데이터<br/>etag: etag |
| Prefix | - | string | 읽어 올 오브젝트의 접두사를 입력합니다. |  |
| 제외할 키 패턴 | - | string | 읽지 않을 오브젝트의 패턴을 입력합니다. |  |
| 처리 완료 오브젝트 삭제 | false | boolean | 속성값이 true일 경우 읽기 완료한 오브젝트를 삭제합니다. |  |

### 메타데이터 필드 사용법

* `메타데이터 포함 여부` 설정 활성화 시 메타데이터 필드가 생성되나, 별도로 일반 필드로 주입하는 작업을 거치지 않는다면 Sink 플러그인에서 노출하지 않습니다.
* 설정 활성화 시 (NHN Cloud) Object Storage 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "오브젝트 내용..."

    // 메타데이터 필드
    // 사용자가 일반 필드로 주입하기 전까지 Sink 플러그인에 노출할 수 없음
    // "[@metadata][s3][last_modified]": 2024-01-05T01:35:50.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][metadata]": {}
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
}
```

* 본 (NHN Cloud) Object Storage Source 플러그인에 필드 추가 옵션이 존재하지만 데이터 인입과 동시에 필드 추가 작업을 수행하지 못합니다.
* 임의의 Filter 플러그인의 공통 설정 중 필드 추가 옵션을 통해 일반 필드로 추가합니다.
* 필드 추가 옵션 예시
```js
{
    "last_modified": "%{[@metadata][s3][last_modified]}"
    "content_length": "%{[@metadata][s3][content_length]}"
    "key": "%{[@metadata][s3][key]}"
    "content_type": "%{[@metadata][s3][content_type]}"
    "metadata": "%{[@metadata][s3][metadata]}"
    "etag": "%{[@metadata][s3][etag]}"
}
```

* alter(필드 추가 옵션) 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "오브젝트 내용..."
    "last_modified": 2024-01-05T01:35:50.000Z
    "content_length": 220
    "key": "{filename}"
    "content_type": "text/plain"
    "metadata": {}
    "etag": "\"56ad65461e0abb907465bacf6e4f96cf\""

    // 메타데이터 필드
    // "[@metadata][s3][last_modified]": 2024-01-05T01:35:50.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][metadata]": {}
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
}
```

### 코덱별 메시지 인입

#### 미선택 혹은 plain

``` js
{
    "message":"{\\\"S3\\\":\\\"Storage\\\", \\\"Read\\\": \\\"Object\\\", \\\"Result\\\": \\\"Data\\\"}"
}
```

#### json

``` js
{"S3":"Storage", "Read": "Object", "Result": "Data"}
```

## Source > (Amazon) S3

### 노드 설명

* S3로부터 데이터를 입력 받는 노드입니다.
* 오브젝트 생성 시간을 기준으로 가장 빨리 생성된 오브젝트부터 데이터를 읽습니다.

### 실행 모드
* STREAMING: `리스트 갱신 주기`마다 오브젝트 리스트를 갱신하며, 새롭게 추가된 오브젝트들을 읽어 데이터를 처리합니다.
* BATCH: 플로우 시작 시점에 오브젝트 리스트를 한번 갱신한 뒤, 오브젝트들을 읽어 데이터를 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 엔드포인트 | - | string | S3 저장소 엔드포인트를 입력합니다. | HTTP, HTTPS URL 형태만 입력 가능합니다. |
| 버킷 | - | string | 데이터를 읽을 버킷 이름을 입력합니다. |  |
| 리전 | - | string | 저장소에 설정된 리전 정보를 입력합니다. |  |
| 세션 토큰 | - | string | AWS 세션 토큰을 입력합니다. |  |
| 비밀 키 | - | string | S3가 발급한 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | - | string | S3가 발급한 자격 증명 액세스 키를 입력합니다. |  |
| 리스트 갱신 주기 | - | number | 버킷에 포함된 오브젝트 리스트 갱신 주기를 입력합니다. |  |
| 메타데이터 포함 여부 | - | boolean | S3 오브젝트의 메타데이터를 키로 포함할지 여부를 결정합니다. 메타데이터 필드를 Sink 플러그인에 노출하기 위해서는 filter 노드 유형을 조합해야 합니다(하단 가이드 참조). | 생성되는 필드는 다음과 같습니다.<br/>server_side_encryption: 서버 측 암호화 알고리즘<br/>last_modified: 오브젝트가 마지막으로 수정된 시간<br/>content_length: 오브젝트 크기<br/>key: 오브젝트 이름<br/>content_type: 오브젝트 형식<br/>metadata: 메타데이터<br/>etag: etag |
| Prefix | - | string | 읽어 올 오브젝트의 접두사를 입력합니다. |  |
| 제외할 키 패턴 | - | string | 읽지 않을 오브젝트의 패턴을 입력합니다. |  |
| 처리 완료 오브젝트 삭제 | false | boolean | 속성값이 true일 경우 읽기 완료한 오브젝트를 삭제합니다. |  |
| 추가 설정 | - | hash | S3 서버와 연결할 때 사용할 추가적인 설정을 입력합니다. | 사용 가능한 설정의 전체 목록은 다음 링크를 참조하세요.<br/>https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html<br/>예)<br/>{<br/>"force\_path\_style": true<br/>} |

### 메타데이터 필드 사용법

* `메타데이터 포함 여부` 설정 활성화 시 메타데이터 필드가 생성되나, 별도로 일반 필드로 주입하는 작업을 거치지 않는다면 Sink 플러그인에서 노출하지 않습니다.
* 설정 활성화 시 (Amazon) S3 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "오브젝트 내용..."

    // 메타데이터 필드
    // 사용자가 일반 필드로 주입하기 전까지 Sink 플러그인에 노출할 수 없음
    // "[@metadata][s3][server_side_encryption]": "AES256"
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][last_modified]": 2024-01-05T02:27:26.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][metadata]": {}
}
```

* 본 (Amazon) S3 Source 플러그인에 필드 추가 옵션이 존재하지만 데이터 인입과 동시에 필드 추가 작업을 수행하지 못합니다.
* 임의의 Filter 플러그인의 공통 설정 중 필드 추가 옵션을 통해 일반 필드로 추가합니다.
* 필드 추가 옵션 예시
```js
{
    "server_side_encryption": "%{[@metadata][s3][server_side_encryption]}"
    "etag": "%{[@metadata][s3][etag]}"
    "content_type": "%{[@metadata][s3][content_type]}"
    "key": "%{[@metadata][s3][key]}"
    "last_modified": "%{[@metadata][s3][last_modified]}"
    "content_length": "%{[@metadata][s3][content_length]}"
    "metadata": "%{[@metadata][s3][metadata]}"
}
```

* alter(필드 추가 옵션) 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "오브젝트 내용..."
    "server_side_encryption": "AES256"
    "etag": "\"56ad65461e0abb907465bacf6e4f96cf\""
    "content_type": "text/plain"
    "key": "{filename}"
    "last_modified": 2024-01-05T01:35:50.000Z
    "content_length": 220
    "metadata": {}

    // 메타데이터 필드
    // "[@metadata][s3][server_side_encryption]": "AES256"
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][last_modified]": 2024-01-05T02:27:26.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][metadata]": {}
}
```

### 코덱별 메시지 인입

#### 미선택 혹은 plain

``` js
{
    "message":"{\\\"S3\\\":\\\"Storage\\\", \\\"Read\\\": \\\"Object\\\", \\\"Result\\\": \\\"Data\\\"}"
}
```

#### json

``` js
{"S3":"Storage", "Read": "Object", "Result": "Data"}
```

## Source > (Apache) Kafka

### 노드 설명

* Kafka에서 데이터를 수신하는 노드입니다.

### 실행 모드
* STREAMING: 토픽에 새로운 메시지가 도착할 때마다 데이터를 처리합니다.

!!! danger "주의"
    * Kafka 노드는 BATCH 모드를 지원하지 않습니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 브로커 서버 목록 | localhost:9092 | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [bootstrap.servers](https://kafka.apache.org/documentation/#consumerconfigs_bootstrap.servers)<br/>예) 10.100.1.1:9092,10.100.1.2:9092 |
| 컨슈머 그룹 아이디 | dataflow | string | Kafka Consumer Group을 식별하는 ID를 입력합니다. | [group.id](https://kafka.apache.org/documentation/#consumerconfigs_group.id) |
| 내부 토픽 제외 여부 | true | boolean |  | [exclude.internal.topics](https://kafka.apache.org/documentation/#consumerconfigs_exclude.internal.topics)<br/>수신 대상에서 `__consumer_offsets`와 같은 내부 토픽을 제외합니다. |
| 토픽 패턴 | - | string | 메시지를 수신할 Kafka 토픽 패턴을 입력합니다. | 예) `*-messages` |
| 클라이언트 아이디 | dataflow | string | Kafka Consumer를 식별하는 ID를 입력합니다. | [client.id](https://kafka.apache.org/documentation/#consumerconfigs_client.id) |
| 파티션 할당 정책 | - | string | Kafka에서 메시지 수신 시 컨슈머 그룹에 어떻게 파티션을 할당할지 결정합니다. | [partition.assignment.strategy](https://kafka.apache.org/documentation/#consumerconfigs_partition.assignment.strategy)<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| 오프셋 설정 | none | enum | 컨슈머 그룹의 오프셋을 설정하는 기준을 입력합니다. | [auto.offset.reset](https://kafka.apache.org/documentation/#consumerconfigs_auto.offset.reset)<br/>아래 설정 모두 컨슈머 그룹이 이미 존재하는 경우 기존 오프셋을 유지합니다.<br/>none: 컨슈머 그룹이 없으면 오류를 반환합니다.<br/>earliest: 컨슈머 그룹이 없으면 파티션의 가장 오래된 오프셋으로 초기화합니다.<br/>latest: 컨슈머 그룹이 없으면 파티션의 가장 최근 오프셋으로 초기화합니다. |
| 오프셋 커밋 주기 | 5000 | number | 컨슈머 그룹의 오프셋을 갱신할 주기를 입력합니다. | [auto.commit.internal.ms](https://kafka.apache.org/documentation/#consumerconfigs_auto.commit.interval.ms) |
| 오프셋 자동 커밋 여부 | true | boolean |  | [enable.auto.commit](https://kafka.apache.org/documentation/#consumerconfigs_enable.auto.commit) |
| 키 역직렬화 유형 | org.apache.kafka.common.serialization.StringDeserializer | string | 수신하는 메시지의 키를 직렬화할 방법을 입력합니다. | [key.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_key.deserializer) |
| 메시지 역직렬화 유형 | org.apache.kafka.common.serialization.StringDeserializer | string | 수신하는 메시지의 값을 직렬화할 방법을 입력합니다. | [value.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_value.deserializer) |
| 메타데이터 생성 여부 | false | boolean | 속성값이 true일 경우 메시지에 대한 메타데이터 필드를 생성합니다. 메타데이터 필드를 Sink 플러그인에 노출하기 위해서는 filter 노드 유형을 조합해야 합니다(하단 가이드 참조). | 생성되는 필드는 다음과 같습니다.<br/>topic: 메시지를 수신한 토픽<br/>consumer\_group: 메시지를 수신하는 데 사용한 컨슈머 그룹 아이디<br/>partition: 메시지를 수신한 토픽의 파티션 번호<br/>offset: 메시지를 수신한 파티션의 오프셋<br/>key: 메시지 키를 포함하는 ByteBuffer |
| Fetch 최소 크기 | - | number | 한 번의 fetch 요청으로 가져올 데이터의 최소 크기를 입력합니다. | [fetch.min.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.min.bytes) |
| 전송 버퍼 크기 | - | number | 데이터를 전송하는 데 사용하는 TCP send 버퍼의 크기(byte)를 입력합니다. | [send.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_send.buffer.bytes) |
| 재시도 요청 주기 | 100 | number | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [retry.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_retry.backoff.ms) |
| 순환 중복 검사 | true | enum | 메시지의 CRC를 검사합니다. | [check.crcs](https://kafka.apache.org/documentation/#consumerconfigs_check.crcs) |
| 서버 재연결 주기 | 50 | number | 브로커 서버에 연결이 실패했을 때 재시도할 주기를 입력합니다. | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_reconnect.backoff.ms) |
| Poll 타임아웃 | 100 | number | 토픽에서 새로운 메시지를 가져오는 요청에 대한 타임아웃(ms)을 입력합니다. |  |
| 파티션당 Fetch 최대 크기 | - | number | 파티션당 한 번의 fetch 요청으로 가져올 최대 크기를 입력합니다. | [max.partition.fetch.bytes](https://kafka.apache.org/documentation/#consumerconfigs_max.partition.fetch.bytes) |
| 서버 요청 타임아웃 | 30000 | number | 전송 요청에 대한 타임아웃(ms)을 입력합니다. | [request.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_request.timeout.ms) |
| TCP 수신 버퍼 크기 | - | number | 데이터를 읽는 데 사용하는 TCP receive 버퍼의 크기(byte)를 입력합니다. | [receive.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_receive.buffer.bytes) |
| session\_timeout\_ms | - | number | 컨슈머의 세션 타임아웃(ms)을 입력합니다.<br/>컨슈머가 해당 시간 안에 heartbeat를 보내지 못할 경우 컨슈머 그룹에서 제외합니다. | [session.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_session.timeout.ms) |
| 최대 poll 메시지 개수 | - | number | 한 번의 poll 요청으로 가져올 최대 메시지 개수를 입력합니다. | [max.poll.records](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.records) |
| 최대 poll 주기 | - | number | poll 요청 간 최대 주기(ms)를 입력합니다. | [max.poll.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.interval.ms) |
| Fetch 최대 크기 | - | number | 한 번의 fetch 요청으로 가져올 최대 크기를 입력합니다. | [fetch.max.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.bytes) |
| Fetch 최대 대기 시간 | - | number | `Fetch 최소 크기` 설정 만큼의 데이터가 모이지 않은 경우 fetch 요청을 보낼 대기 시간(ms)을 입력합니다. | [fetch.max.wait.ms](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.wait.ms) |
| 컨슈머 헬스체크 주기 | - | number | 컨슈머가 heartbeat를 보내는 주기(ms)를 입력합니다. | [heartbeat.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_heartbeat.interval.ms) |
| 메타데이터 갱신 주기 | - | number | 파티션, 브로커 서버 상태 등을 갱신할 주기(ms)를 입력합니다. | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms) |
| IDLE 타임아웃 | - | number | 데이터 전송이 없는 커넥션을 닫을 대기 시간(ms)을 입력합니다. | [connections.max.idle.ms](https://kafka.apache.org/documentation/#consumerconfigs_connections.max.idle.ms) |

### 메타데이터 필드 사용법

* `메타데이터 생성 여부` 설정 활성화 시 메타데이터 필드가 생성되나, 별도로 일반 필드로 주입하는 작업을 거치지 않는다면 Sink 플러그인에서 노출하지 않습니다.
* 설정 활성화 시 Kafka 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafka 토픽 메시지..."

    // 메타데이터 필드
    // 사용자가 일반 필드로 주입하기 전까지 Sink 플러그인에 노출할 수 없음
    // "[@metadata][kafka][topic]": "my-topic"
    // "[@metadata][kafka][consumer_group]": "my_consumer_group"
    // "[@metadata][kafka][partition]": "1"
    // "[@metadata][kafka][offset]": "123"
    // "[@metadata][kafka][key]": "my_key"
    // "[@metadata][kafka][timestamp]": "-1"
}
```

* 본 Kafka Source 플러그인에 필드 추가 옵션이 존재하지만 데이터 인입과 동시에 필드 추가 작업을 수행하지 못합니다.
* 임의의 Filter 플러그인의 공통 설정 중 필드 추가 옵션을 통해 일반 필드로 추가합니다.
* 필드 추가 옵션 예시
```js
{
    "kafka_topic": "%{[@metadata][kafka][topic]}"
    "kafka_consumer_group": "%{[@metadata][kafka][consumer_group]}"
    "kafka_partition": "%{[@metadata][kafka][partition]}"
    "kafka_offset": "%{[@metadata][kafka][offset]}"
    "kafka_key": "%{[@metadata][kafka][key]}"
    "kafka_timestamp": "%{[@metadata][kafka][timestamp]}"
}
```
* alter(필드 추가 옵션) 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafka 토픽 메시지..."
    "kafka_topic": "my-topic"
    "kafka_consumer_group": "my_consumer_group"
    "kafka_partition": "1"
    "kafka_offset": "123"
    "kafka_key": "my_key"
    "kafka_timestamp": "-1"

    // 메타데이터 필드
    // "[@metadata][kafka][topic]": "my-topic"
    // "[@metadata][kafka][consumer_group]": "my_consumer_group"
    // "[@metadata][kafka][partition]": "1"
    // "[@metadata][kafka][offset]": "123"
    // "[@metadata][kafka][key]": "my_key"
    // "[@metadata][kafka][timestamp]": "-1"
}
```
        
### plain 코덱 예제

#### 입력 메시지

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

#### 출력 메시지

```js
{
    "message": "{\"hello\":\"world\",\"hey\":\"foo\"}"
}
```

### json 코덱 예제

#### 입력 메시지

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

#### 출력 메시지

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

## Source > JDBC

### 노드 설명

* JDBC는 주어진 주기로 DB에 쿼리를 실행하여 결과를 가져오는 노드입니다.
* MySQL, MS-SQL, PostgreSQL, MariaDB, Oracle 드라이버를 지원합니다.

### 실행 모드
* STREAMING: `쿼리 실행 주기`마다 쿼리를 실행하여 데이터를 처리합니다.
* BATCH: 플로우 시작 시점에 쿼리를 한번 실행하여 데이터를 처리한 후 플로우를 종료합니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 사용자 | - | string | DB 사용자를 입력합니다. |  |
| 연결 문자열 | - | string | DB 연결 정보를 입력합니다. | 예) `jdbc:mysql://my.sql.endpoint:3306/my_db_name` |
| 비밀번호 | - | string | 사용자 비밀번호를 입력합니다. |  |
| 쿼리 | - | string | 메시지를 생성할 쿼리를 작성합니다. |  |
| 컬럼 소문자화 변환 여부 | true | boolean | 쿼리 결과로 얻는 컬럼명을 소문자화할지를 결정합니다. | |
| 쿼리 실행 주기 | `* * * * *` | string | 쿼리의 실행 주기를 cron-like 표현으로 입력합니다. |  |
| 트래킹 컬럼 | - | string | 추적할 컬럼을 선택합니다. | 사전 정의된 파라미터 `:sql_last_value`로 마지막 쿼리 결과에서 추적할 컬럼에 해당하는 값을 사용할 수 있습니다.<br>아래 쿼리 작성법을 참고하세요. |
| 트래킹 컬럼 종류 | number | string | 추적할 컬럼의 데이터 종류를 선택합니다. | 예) `numeric` or `timestamp` |
| 시간대 | - | string | timestamp 타입의 컬럼을 human-readable 문자열로 변환할 때 사용하는 시간대를 정의합니다. | 예) `Asia/Seoul` |
| 페이징 적용 여부 | false | boolean | 쿼리에 페이징을 적용할지 여부를 결정합니다. | 페이징이 적용되면 쿼리가 여러 개로 쪼개져서 실행되며, 순서는 보장되지 않습니다. |
| 페이지 크기 | - | number | 페이징이 적용된 쿼리에서, 한 번에 쿼리 할 페이지 크기를 결정합니다. |  |

### 쿼리 작성법

* `:sql_last_value` 를 통해 가장 마지막에 실행된 쿼리 결과에서 `트래킹 컬럼`에 해당하는 값을 사용할 수 있습니다(초깃값은 `트래킹 컬럼 종류`가 `numeric`이라면 `0`, `timestamp`라면 `1970-01-01 00:00:00`).

``` sql
SELECT * FROM MY_TABLE WHERE id > :sql_last_value
```

* 특정 조건을 추가하고 싶다면 조건과 더불어서 `:sql_last_value`를 추가합니다.

```sql
SELECT * FROM MY_TABLE WHERE id > :sql_last_value and id > custom_value order by id ASC
```

### 코덱별 메시지 인입

#### 미선택 혹은 plain

``` js
{
    "id": 1,
    "name": "dataflow",
    "deleted": false
}
```

## Filter

* 인입된 데이터를 어떻게 처리할지 정의하는 노드 유형입니다.

### Filter 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 아이디 | - | string | 노드의 아이디를 설정합니다<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |
| 태그 추가 | - | array of strings | 각 메시지에 태그를 추가합니다. |  |
| 태그 삭제 | - | array of strings | 각 메시지에 주어진 태그를 삭제합니다. |  |
| 필드 삭제 | - | array of strings | 각 메시지의 필드를 삭제합니다. |  |
| 필드 추가 | - | hash | 커스텀 필드를 추가할 수 있습니다.<br/>`%{[depth1_field]}`로 각 필드의 값을 가져와 필드를 추가할 수 있습니다. |  |

## Filter > Alter

### 노드 설명

* 메시지 필드 값을 다른 값으로 변경합니다.
* 최상위 필드만 변경할 수 있습니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 필드 변경 | - | array of strings | 필드 값을 주어진 값과 비교하여 같을 경우 해당 필드의 값을 주어진 값으로 수정합니다. |  |
| 필드 덮어쓰기 | - | array of strings | 필드 값을 주어진 값과 비교하여 같을 경우 다른 필드의 값을 주어진 값으로 수정합니다. |  |
| Coalesce | - | array of strings | 하나의 필드에 뒤이어 오는 필드 중 처음으로 null이 아닌 값을 할당합니다. |  |

### 설정 적용 순서

* 각 설정은 속성 설명에 기재된 순서대로 적용됩니다.

#### 조건

* 필드 변경 → `["logType", "ERROR", "FAIL"]`
* 필드 덮어쓰기 → `["logType", "FAIL", "isBillingTarget", "false"]`

#### 입력 메시지

```json
{
    "logType": "ERROR",
    "isBillingTarget": "true"
}
```

#### 출력 메시지

```json
{
    "logType": "FAIL",
    "isBillingTarget": "false"
}
```

### 필드 덮어쓰기 예제

#### 조건

* 필드 덮어쓰기 → `["logType", "ERROR", "isBillingTarget", "false"]`

#### 입력 메시지

```json
{
    "logType": "ERROR", 
    "isBillingTarget": "true"
}
```

#### 출력 메시지

```json
{
    "logType": "ERROR",
    "isBillingTarget": "false"
}
```

### 필드 변경 예제

#### 조건

* 필드 변경 → `["reason", "CONNECTION_TIMEOUT", "MONGODB_CONNECTION_TIMEOUT"]`

#### 입력 메시지

```js
{
    "reason": "CONNECTION_TIMEOUT"
}
```

#### 출력 메시지

```js
{
    "reason": "MONGODB_CONNECTION_TIMEOUT"
}
```

### Coalesce 예제

#### 조건

* Coalesce → `["reason", "%{webClientReason}", "%{mongoReason}", "%{redisReason}"]`

#### 입력 메시지

```js
{
    "mongoReason": "COLLECTION_NOT_FOUND"
}
```

#### 출력 메시지

```js
{
    "reason": "COLLECTION_NOT_FOUND",
    "mongoReason": "COLLECTION_NOT_FOUND"
}
```

## Filter > Cipher

### 노드 설명

* 메시지 필드 값을 암복호화하는 노드입니다.
* 암호화 키는 Secure Key Manager 대칭 키를 참조합니다.
    * Secure Key Manager 대칭 키는 Secure Key Manager 웹 콘솔 또는 Secure Key Manager의 키 추가 API를 통해 생성할 수 있습니다.
    * 한 플로우에 여러 Cipher 노드가 포함되더라도 모든 Cipher 노드는 반드시 하나의 Secure Key Manager 키 레퍼런스만 참조할 수 있습니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 모드 | - | enum | 암호화 모드와 복호화 모드 중 선택합니다. | 목록 중에 하나를 선택합니다. |
| 앱키 | - | string | 암/복호화에 사용할 키를 저장한 SKM 앱키를 입력합니다. |  |
| 키 ID | - | string | 암/복호화에 사용할 키를 저장한 SKM 키 ID를 입력합니다. |  |
| 키 버전 | - | string | 암/복호화에 사용할 키를 저장한 SKM 키 버전을 입력합니다. |  |
| 암/복호화 키 길이 | 16 | number | 암/복호화 키의 길이를 입력합니다. |  |
| IV 랜덤 길이 | - | number | Initial Vector의 random bytes 길이를 입력합니다. |  |
| 소스 필드 | - | string | 암/복호화할 필드명을 입력합니다. |  |
| 저장할 필드 | - | string | 암/복호화 결과를 저장할 필드명을 입력합니다. |  |

### encrypt 예제

#### 조건

* mode → `encrypt`
* 앱키 → `SKM 앱키`
* 키 ID → `SKM 대칭키 ID`
* 키 버전 → `1`
* IV 랜덤 길이 → `16`
* 소스 필드 → message
* 저장할 필드 → encrypted\_message

#### 입력 메시지

``` js
{
    "message": "this is plain message"
}
```

#### 출력 메시지

``` js
{
    "message": "this is plain message",
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

### decrypt 예제

#### 조건

* mode → `decrypt`
* 앱키 → `SKM 앱키`
* 키 ID → `SKM 대칭키 ID`
* 키 버전 → `1`
* IV 랜덤 길이 → `16`
* 소스 필드 → message
* 저장할 필드 → decrypted\_message

#### 입력 메시지

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

#### 출력 메시지

``` js
{
    "decrypted_message": "this is plain message",
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

## Filter > (Logstash) Grok

### 노드 설명

* 문자열을 정해진 규칙에 맞게 파싱하여 각 설정된 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Match | - | hash | 파싱할 문자열의 정보를 입력합니다. |  |
| 패턴 정의 | - | hash | 파싱할 토큰의 규칙의 사용자 정의 패턴을 정규표현식으로 입력합니다. | 시스템 정의 패턴은 아래 링크를 확인하세요.<br/>https://github.com/logstash-plugins/logstash-patterns-core/blob/main/patterns/legacy/grok-patterns |
| 실패 태그 | - | array of strings | 문자열 파싱에 실패할 경우 정의할 태그명을 입력합니다. |  |
| 타임아웃 | 30000 | number | 문자열 파싱이 될 때까지 기다리는 시간을 입력합니다. |  |
| 덮어쓰기 | - | array of strings | 파싱 후 지정된 필드에 값을 쓸 때 해당 필드에 이미 값이 정의되어 있을 경우 덮어쓸 필드명들을 입력합니다. |  |
| 이름이 지정된 값만 저장 | true | boolean | 속성값이 true일 경우 이름이 지정되지 않은 파싱 결과를 저장하지 않습니다. |  |
| 빈 문자열 캡처 | false | boolean | 속성값이 true일 경우 빈 문자열도 필드에 저장합니다. |  |
| Match 시 종료 여부 | true | boolean | 속성값이 true일 경우 grok match 결과가 참이면 플러그인을 종료합니다. |  |

### Grok 파싱 예제

#### 조건

* Match → `{ "message": "%{IP:clientip} %{HYPHEN} %{USER} \[%{HTTPDATE:timestamp}\] \"%{WORD:verb} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}\" %{NUMBER:response} %{NUMBER:bytes}" }`
* 패턴 정의 → `{ "HYPHEN": "-*" }`

#### 입력 메시지

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326"
}
```

#### 출력 메시지

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326",
    "timestamp": "10/Oct/2000:13:55:36 -0700",
    "clientip": "127.0.0.1",
    "verb": "GET",
    "httpversion": "1.0",
    "response": "200",
    "bytes": "2326",
    "request": "/apache_pb.gif"
}
```

## Filter > CSV

### 노드 설명

* CSV 형식의 메시지를 파싱해 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 저장할 필드 | - | string | CSV 파싱 결과를 저장할 필드명를 입력합니다. |  |
| Quote | " | string | 컬럼 필드를 나누는 문자를 입력합니다. |  |
| 첫 행 무시 여부 | false | boolean | 속성값이 true일 경우 읽은 데이터 중 첫 행에 입력된 컬럼 이름을 무시합니다. |  |
| 컬럼 | - | array of strings | 컬럼 이름을 입력합니다. |  |
| 구분자 | , | string | 컬럼을 구분할 문자열을 입력합니다. |  |
| 소스 필드 | message | string | CSV 파싱할 필드명을 입력합니다. |  |
| 스키마 | - | hash | 각 컬럼의 이름과 자료형을 dictionary 형태로 입력합니다. | 컬럼에 정의된 필드와 별개로 등록합니다.<br/>자료형은 기본적으로 string이며, 다른 자료형으로 변환이 필요할 경우 스키마 설정을 활용합니다.<br/>가능한 자료형은 다음과 같습니다.<br/>integer, float, date, date_time, boolean |

### 자료형 없는 CSV 파싱 예제

#### 조건

* 소스 필드 → `message`
* 컬럼 → `["one", "two", "t hree"]`

#### 입력 메시지

```js
{
    "message": "hey,foo,\\\"bar baz\\\""
}
```

#### 출력 메시지

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```

### 자료형 없는 CSV 파싱 예제

#### 조건

* 소스 필드 → `message`
* 컬럼 → `["one", "two", "t hree"]`

#### 입력 메시지

```js
{
    "message": "hey,foo,\\\"bar baz\\\""
}
```

#### 출력 메시지

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```

### 자료형 변환이 필요한 CSV 파싱 예제

#### 조건

* 소스 필드 → `message`
* 컬럼 → `["one", "two", "t hree"]`
* 스키마 → `{"two": "integer", "t hree": "boolean"}`

#### 입력 메시지

```js
{
    "message": "\\\"wow hello world!\\\", 2, false"
}
```

#### 출력 메시지

```js
{
    "message": "\\\"wow hello world!\\\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### 노드 설명

* JSON 문자열을 파싱하여 지정된 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 소스 필드 | message | string | JSON 문자열을 파싱할 필드명을 입력합니다. |  |
| 저장할 필드 | - | string | JSON 파싱 결과를 저장할 필드명을 입력합니다.<br/>만약 속성값을 지정하지 않을 경우 root 필드에 결과를 저장합니다. |  |

### JSON 파싱 예제

#### 조건

* 소스 필드 → `message`
* 저장할 필드 → `json_parsed_messsage`

#### 입력 메시지

```js
{
    "message": "{\\\"json\\\": \\\"parse\\\", \\\"example\\\": \\\"string\\\"}"
}
```

#### 출력 메시지

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": "string"
    },
    "message": "uuid test message"
}
```

## Filter > (Logstash) Grok

### 노드 설명

* 문자열을 정해진 규칙에 맞게 파싱하여 각 설정된 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Match | - | json | 파싱할 문자열의 정보를 입력합니다. |  |
| 패턴 정의 | - | json | 파싱할 토큰의 규칙의 사용자 정의 패턴을 정규표현식으로 입력합니다. | 시스템 정의 패턴은 아래 링크를 확인하세요.<br/>http://grokdebug.herokuapp.com/patterns |
| 실패 태그 | - | array of strings | 문자열 파싱에 실패할 경우 정의할 태그명을 입력합니다. |  |
| 타임아웃 | 30000 | number | 문자열 파싱이 될 때까지 기다리는 시간을 입력합니다. |  |
| 덮어쓰기 | - | array of strings | 파싱 후 지정된 필드에 값을 쓸 때 해당 필드에 이미 값이 정의되어 있을 경우 덮어쓸 필드명들을 입력합니다. |  |
| 이름이 지정된 값만 저장 | - | boolean | 이름이 지정되지 않은 파싱 결과를 저장할지 여부를 선택합니다. |  |
| 빈 문자열 캡처 | - | boolean | 빈 문자열도 필드에 저장할지 여부를 선택합니다. |  |

### Grok 파싱 예제

#### 조건

* Match → `{ "message": "%{IP:clientip} %{HYPHEN} %{USER} \[%{HTTPDATE:timestamp}\] \"%{WORD:verb} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}\" %{NUMBER:response} %{NUMBER:bytes}" }`
* 패턴 정의 → `{ "HYPHEN": "-*" }`

#### 입력 메시지

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326"
}
```

#### 출력 메시지

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326",
    "timestamp": "10/Oct/2000:13:55:36 -0700",
    "clientip": "127.0.0.1",
    "verb": "GET",
    "httpversion": "1.0",
    "response": "200",
    "bytes": "2326",
    "request": "/apache_pb.gif"
}
```

## Filter > Date

### 노드 설명

* Date 문자열을 파싱하여 timestamp 형태로 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 소스 필드 | - | string | 문자열을 가져오기 위한 필드명을 입력합니다. |  |
| 형식 | - | array of strings | 문자열을 가져오기 위한 형식을 입력합니다. | 사전 정의된 형식은 다음과 같습니다.<br/>ISO8601, UNIX, UNIX_MS, TAI64N |
| Locale | - | Date 문자열 분석을 위해 사용할 Locale을 입력합니다. | ex) en, en-US, ko-kr |
| 저장할 필드 | - | string | Date 문자열 파싱 결과를 저장할 필드명을 입력합니다. |  |
| 실패 태그 | - | array of strings | Date 문자열 파싱에 실패했을 경우 정의할 태그명을 입력합니다. |  |
| 시간대 | - | string | 날짜의 시간대를 입력합니다. |  |

### Date 문자열 파싱 예제

#### 조건

* Match → `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* 저장할 필드 → `time`
* 시간대 → `Asia/Seoul`

#### 입력 메시지

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### 출력 메시지

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### 노드 설명

* UUID를 생성하여 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| UUID 저장 필드 | - | string | UUID 생성 결과값을 저장할 필드명을 입력합니다. |  |
| 덮어쓰기 | - | boolean | 지정된 필드명에 값이 존재할 경우 이를 덮어쓸지 여부를 선택합니다. |  |

### UUID 생성 예제

#### 조건

* UUID 저장 필드 → `userId`

#### 입력 메시지

```js
{
    "message": "uuid test message"
}
```

#### 출력 메시지

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Split

### 노드 설명

* 하나의 메시지를 여러 메시지로 분할하는 노드입니다.
* 설정에 따라 파싱한 결과를 바탕으로 메시지를 분할합니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 소스 필드 | - | string | 메시지를 분리할 필드명을 입력합니다. |  |
| 저장할 필드 | - | string | 분리된 메시지를 저장할 필드명을 입력합니다. |  |
| 구분자 | `\n` | string |  |  |

### 기본 메시지 분할 예제

#### 조건

* 소스 필드 → `message`

#### 입력 메시지

```js
{
    "message": [
        {"number": 1},
        {"number": 2}
    ]
}
```

#### 출력 메시지

```js
{
    "message": [
        {"number": 1},
        {"number": 2}
    ],
    "number": 1
}
```
```js
{
    "message": [
        {"number": 1},
        {"number": 2}
    ],
    "number": 2
}
```

### 문자열 파싱 후 분할 예제

#### 조건

* 소스 필드 → `message`
* 구분자 → `,`

#### 입력 메시지

```js
{
    "message": "1,2"
}
```

#### 출력 메시지

```js
{
    "message": "1"
}
```
```js
{
    "message": "2"
}
```

### 문자열 파싱 후 다른 필드로 분할 예제

#### 조건

* 소스 필드 → `message`
* 저장할 필드 → `target`
* 구분자 → `,`

#### 입력 메시지

```js
{
    "message": "1,2"
}
```

#### 출력 메시지

```js
{
    "message": "1,2",
    "target": "1"
}
```
```js
{
    "message": "1,2",
    "target": "2"
}
```

## Filter > Truncate

### 노드 설명

* JSON 문자열을 파싱하여 지정된 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Byte 길이 | - | number | 문자열을 표현할 최대 byte 길이를 입력합니다. |  |
| 소스 필드 | - | string | truncate 대상 필드명을 입력합니다. |  |

### JSON 파싱 예제

#### 조건

* Byte 길이 → 10
* 소스 필드 → `message`

#### 입력 메시지

```js
{
    "message": "이 메시지는 너무 깁니다."
}
```

#### 출력 메시지

```js
{
    "message": "이 메세"
}
```

## Filter > Mutate

### 노드 설명

* 필드의 값을 변형하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 기본값 설정 | - | hash | null 값을 기본값으로 대체합니다. |  |
| 필드명 변경 | - | hash | 필드 이름을 변경합니다. |  |
| 필드값 갱신 | - | hash | 필드 값을 새 값으로 교체합니다. 필드가 없다면 아무런 동작도 하지 않습니다. |  |
| 값 대체 | - | hash | 필드 값을 새 값으로 교체. 필드가 없다면 새로 생성합니다.  |  |
| 타입 변환 | - | hash | 필드 값을 다른 타입으로 변환합니다. | 지원하는 타입은 integer, interger_eu, float, float_eu, string, boolean입니다. |
| 문자열 치환 | - | array | 정규식으로 문자열 일부를 교체합니다. |  |
| 대문자 변환 | - | array | 필드의 문자열을 대문자로 변경합니다. |  |
| 첫 글자 대문자화 | - | array | 필드의 첫 글자를 대문자로 변환하고 나머지는 소문자로 변환합니다. |  |
| 소문자 변환 | - | array | 대상 필드의 문자열을 소문자로 변경합니다. |  |
| 공백 제거 | - | array | 필드의 문자열 앞뒤 공백을 제거합니다. |  |
| 문자열 분할 | - | hash | 구분자를 이용해 문자열을 배열로 분할합니다. |  |
| 배열 결합 | - | hash | 구분자를 이용하여 배열의 요소를 하나의 문자열로 합칩니다. |  |
| 필드 병합 | - | hash | 두 필드를 합칩니다. |  |
| 필드 복사 | - | hash | 기존 필드를 다른 필드로 복사합니다. 필드가 존재한다면 덮어씁니다. |  |
| 실패 태그 | _mutate_error | string | 오류가 발생한 경우 정의할 태그를 입력합니다. |  |

### 설정 적용 순서
* 각 설정은 속성 설명에 기재된 순서대로 적용됩니다.

#### 조건

* 필드값 갱신 → `{"fieldname": "new value"}`
* 대문자 변환 → `["fieldname"]`

#### 입력 메시지

```json
{
    "fieldname": "old value"
}
```

#### 출력 메시지

```json
{
    "fieldname": "NEW VALUE"
}
```

### 기본값 설정 예제

#### 조건

```json
{
  "fieldname": "default_value"
}
```

#### 입력 메시지

```json
{
  "fieldname": null
}
```

#### 출력 메시지

```json
{
  "fieldname": "default_value"
}
```

### 필드명 변경 예제

#### 조건
```json
{
  "fieldname": "changed_fieldname"
}
```

#### 입력 메시지

```json
{
  "fieldname": "Hello World!"
}
```

#### 출력 메시지

```json
{
  "changed_fieldname": "Hello World!"
}
```

### 필드값 갱신 예제

#### 조건

```json
{
  "fieldname": "%{other_fieldname}: %{fieldname}",
  "not_exist_fieldname": "DataFlow"
}
```

#### 입력 메시지

```json
{
  "fieldname": "Hello World!",
  "other_fieldname": "DataFlow"
}
```

#### 출력 메시지

```json
{
  "fieldname": "DataFlow: Hello World!",
  "other_fieldname": "DataFlow"
}
```

### 값 대체 예제

#### 조건

```json
{
  "fieldname": "%{other_fieldname}: %{fieldname}",
  "not_exist_fieldname": "DataFlow"
}
```

#### 입력 메시지

```json
{
  "fieldname": "Hello World!",
  "other_fieldname": "DataFlow"
}
```

#### 출력 메시지

```json
{
  "fieldname": "DataFlow: Hello World!",
  "other_fieldname": "DataFlow",
  "not_exist_fieldname": "DataFlow"
}
```

### 타입 변환 예제

#### 조건

```
{
  "message1": "integer",
  "message2": "boolean"
}
```

#### 입력 메시지

```json
{
  "message1": "1000",
  "message2": "true"
}
```

#### 출력 메시지

```json
{
    "message1": 1000,
    "message2": true
}
```

#### 타입 설명

* integer
  * 문자열을 정수형으로 변환합니다. 콤마로 구분된 문자열을 지원합니다. 소수점 이하 데이터는 제거됩니다.
    * 예: "1,000.5" -> `1000`
  * 실수형 데이터를 정수형으로 변환합니다. 소수점 이하 데이터는 제거됩니다.
  * boolean형 데이터를 정수형으로 변환합니다. `true`는 `1`, `false`는 `0`으로 변환됩니다.
* integer_eu
  * 데이터를 정수형으로 변환합니다. 점으로 구분된 문자열을 지원합니다. 소수점 이하 데이터는 제거됩니다. 
    * 예: "1.000,5" -> `1000`
  * 실수형과 boolean형 데이터는 integer와 동일합니다.
* float
  * 정수형 데이터를 실수형으로 변환합니다.
  * 문자열을 실수형으로 변환합니다. 콤마로 구분된 문자열을 지원합니다.
    * 예: "1,000.5" -> `1000.5`
  * boolean형 데이터를 정수형으로 변환합니다. `true`는 `1.0`, `false`는 `0.0`으로 변환됩니다.
* float_eu
  * 데이터를 실수형으로 변환합니다. 점으로 구분된 문자열을 지원합니다.
    * 예: "1.000,5" -> `1000.5`
  * 실수형과 boolean형 데이터는 float와 동일합니다.
* string
  * 데이터를 UTF-8 인코딩의 문자열로 변환합니다.
* boolean
  * 정수형 데이터를 boolean형으로 변환합니다. `1`은 `true`, `0`은 `false`로 변환됩니다.
  * 실수형 데이터를 boolean형으로 변환합니다. `1.0`은 `true`, `0.0`은 `false`로 변환됩니다.
  * 문자열을 boolean형으로 변환합니다. `"true"`, `"t"`, `"yes"`, `"y"`, `"1"`, `"1.0"`은 `true`, `"false"`, `"f"`, `"no"`, `"n"`, `"0"`, `"0.0"`은 `false`로 변환됩니다. 빈 문자열은 `false`로 변환됩니다.
* 배열 데이터는 각 요소가 위 설명에 맞게 변환됩니다.

### 문자열 치환 예제

#### 조건

```json
["fieldname", "/", "_", "fieldname2", "[\\?#-]", "."]
```
* `fieldname` 필드의 문자열 값에서 `/`를 `_`로, `fieldname2` 필드의 문자열 값에서 `\`, `?`, `#`, `-`를 `.`으로 치환합니다.

#### 입력 메시지
```json
{
  "fieldname": "Hello/World",
  "fieldname2": "Hello\\?World#Test-123"
}
```

#### 출력 메시지
```json
{
  "fieldname": "Hello_World",
  "fieldname2": "Hello.World.Test.123"
}
```

### 대문자 변환 예제

#### 조건

```json
["fieldname"]
```

#### 입력 메시지

```json
{
  "fieldname": "hello world"
}
```

#### 출력 메시지

```json
{
  "fieldname": "HELLO WORLD"
}
```

### 첫 글자 대문자화 예제

#### 조건

```json
["fieldname"]
```

#### 입력 메시지

```json
{
  "fieldname": "hello world"
}
```

#### 출력 메시지

```json
{
  "fieldname": "Hello world"
}
```

### 소문자 변환 예제
#### 조건

```json
["fieldname"]
```

#### 입력 메시지

```json
{
  "fieldname": "HELLO WORLD"
}
```

#### 출력 메시지

```json
{
  "fieldname": "hello world"
}
```

### 공백 제거 예제

#### 조건

```json
["field1", "field2"]
```

#### 입력 메시지

```json
{
  "field1": "Hello World!   ",
  "field2": "   Hello DataFlow!"
}
```

#### 출력 메시지

```json
{
  "field1": "Hello World!",
  "field2": "Hello DataFlow!"
}
```

### 문자열 분할 예제

#### 조건

```json
{
  "fieldname": ","
}
```

#### 입력 메시지

```json
{
  "fieldname": "Hello,World"
}
```

#### 출력 메시지

```json
{
  "fieldname": ["Hello", "World"]
}
```

### 배열 결합 예제

#### 조건

```json
{
  "fieldname": ","
}
```

#### 입력 데이터

```json
{
  "fieldname": ["Hello", "World"]
}
```

#### 출력 메시지

```json
{
  "fieldname": "Hello,World"
}
```

### 필드 병합 예제
#### 조건

```json
{
  "array_data1": "string_data1",
  "string_data2": "string_data1",
  "json_data1": "json_data2"
}
```

#### 입력 메시지

```json
{
  "array_data1": ["array_data1"],
  "string_data1": "string_data1",
  "string_data2": "string_data2",
  "json_data1": {"json_field1": "json_data1"},
  "json_data2": {"json_field2": "json_data2"}
}
```

#### 출력 메시지

```json
{
  "array_data1": ["array_data1", "string_data1"],
  "string_data1": "string_data1",
  "string_data2": ["string_data2", "string_data1"],
  "json_data1": {"json_field2" : "json_data2", "json_field1": "json_data1"},
  "json_data2": {"json_field2": "json_data2"}
}
```
* array + array = 두 array를 병합
* array + string = array에 string을 추가
* string + string = 두 string을 요소로 하는 array로 변환
* json + json = json 병합

### 필드 복사 예제

#### 조건

```json
{
  "source_field": "dest_field"
}
```

#### 입력 메시지

```json
{
  "source_field": "Hello World!"
}
```

#### 출력 메시지

```json
{
  "source_field": "Hello World!",
  "dest_field": "Hello World!"
}
```

## Sink

* Filter 작업을 마친 데이터를 적재할 엔드포인트를 정의하는 노드 유형입니다.

### Sink 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |

## Sink > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage에 데이터를 업로드하는 노드입니다.
* OBS에 작성되는 오브젝트는 기본적으로 다음 경로 포맷에 맞게 출력됩니다.
    * `/{container_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 리전 | - | enum | Object Storage 상품의 리전을 입력합니다. |  |
| 버킷 | - | string | 버킷 이름을 입력합니다. |  |
| 비밀 키 | - | string | S3 API 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | - | string | S3 API 자격 증명 액세스 키를 입력합니다. |  |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | 오브젝트 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix 시간 필드 | @timestamp | string | Prefix에 적용할 시간 필드를 입력합니다. |  |
| Prefix 시간 필드 타입 | DATE_FILTER_RESULT | enum | Prefix에 적용할 시간 필드의 타입을 입력합니다. |  |
| Prefix 시간대 | UTC | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다. |  |
| Prefix 시간 적용 fallback  | _prefix_datetime_parse_failure | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다. |  |
| 인코딩 | none | enum | 인코딩 여부를 입력합니다. gzip 인코딩을 사용할 수 있습니다. |  |
| 오브젝트 로테이션 정책 | size\_and\_time | enum | 오브젝트의 생성 규칙을 결정합니다. | size\_and\_time: 오브젝트의 크기와 시간을 이용하여 결정<br/>size: 오브젝트의 크기를 이용하여 결정<br/>time: 시간을 이용하여 결정 |
| 기준 시각 | 15 | number | 오브젝트를 분할할 기준이 될 시간을 설정합니다. | 오브젝트 로테이션 정책이 size\_and\_time 또는 time인 경우 설정 |
| 기준 오브젝트 크기 | 5242880 | number | 오브젝트를 분할할 기준이 될 크기를 설정합니다. | 오브젝트 로테이션 정책이 size\_and\_time 또는 size인 경우 설정 |

### json 코덱 출력 예제

#### 조건

* 리전 → `KR1`
* 버킷 → `obs-test-container`
* 액세스 키 → `******`
* 비밀 키 → `******`

#### 입력 메시지

``` json
{"hidden":"Hello DataFlow!","message":"Hello World", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### 출력값

* 경로

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* 출력 메시지

``` json
{"@timestamp":"2022-11-21T07:49:20.000Z","host":"755b65d82bd0","hidden":"Hello DataFlow!","@version":"1","sequence":0,"message":"Hello World"}
```

### plain 코덱 출력 예제 - message 필드 존재할 경우

#### 조건

* 리전 → `KR1`
* 버킷 → `obs-test-container`
* 액세스 키 → `******`
* 비밀 키 → `******`

#### 입력 메시지

``` json
{
    "message": "Hello World!",
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 출력값

* 경로

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* 출력 메시지

```
2022-11-21T07:49:20.000Z e0e40e03dd94 Hello World
```

### plain 코덱 출력 예제 - message 필드 존재하지 않을 경우

#### 조건

* 리전 → `KR1`
* 버킷 → `obs-test-container`
* 액세스 키 → `******`
* 비밀키 → `******`

#### 입력 메시지

``` json
{
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 출력값

* 경로

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* 출력 메시지

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
```

### Parquet 코덱 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| parquet 압축 코덱 | SNAPPY | enum | parquet 파일 변환 시 사용할 압축 코덱을 입력합니다. | [참조](https://parquet.apache.org/docs/file-format/data-pages/compression/) |

### Prefix 예시 - 필드

#### 조건

* 버킷 → `obs-test-container`
* Prefix → `/dataflow/%{deployment}`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로

```
/obs-test-container/dataflow/production/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

### Prefix 예시 - 시간

#### 조건

* 버킷 → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix 시간 필드 → `logTime`
* Prefix 시간 필드 타입 → `ISO8601`
* Prefix 시간대 → `Asia/Seoul`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로

```
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

### Prefix 예시 - 시간 적용 실패한 경우

#### 조건

* 버킷 → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix 시간 필드 → `logTime`
* Prefix 시간 필드 타입 → `TIMESTAMP_SEC`
* Prefix 시간대 → `Asia/Seoul`
* Prefix 시간 적용 fallback → `_failure`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로

```
/obs-test-container/_failure/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

## Sink > (Amazon) S3

### 노드 설명

* Amazon S3에 데이터를 업로드하는 노드입니다.

### 속성 설명
| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 리전 | - | enum | S3 상품의 리전을 입력합니다. | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| 버킷 | - | string | 버킷 이름을 입력합니다. |  |
| 액세스 키 | - | string | S3 API 자격 증명 액세스 키를 입력합니다. |  |
| 비밀 키 | - | string | S3 API 자격 증명 비밀 키를 입력합니다. |  |
| 서명 버전 | - | enum | AWS 요청을 서명할 때 사용할 버전을 입력합니다. |  |
| 세션 토큰 | - | string | AWS 임시 자격 증명을 위한 세션 토큰을 입력합니다. | [세션 토큰 가이드](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) |
| Prefix | - | string | 오브젝트 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix 시간 필드 | @timestamp | string | Prefix에 적용할 시간 필드를 입력합니다. |  |
| Prefix 시간 필드 타입 | DATE_FILTER_RESULT | enum | Prefix에 적용할 시간 필드의 타입을 입력합니다. |  |
| Prefix 시간대 | UTC | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다. |  |
| Prefix 시간 적용 fallback  | _prefix_datetime_parse_failure | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다. |  |
| 스토리지 클래스 | STANDARD | enum | 오브젝트를 업로드할 때 사용할 스토리지 클래스를 설정합니다. | [스토리지 클래스 가이드](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| 인코딩 | none | enum | 인코딩 여부를 입력합니다. gzip 인코딩을 사용할 수 있습니다. |  |
| 오브젝트 로테이션 정책 | size\_and\_time | enum | 오브젝트의 생성 규칙을 결정합니다. | size\_and\_time: 오브젝트의 크기와 시간을 이용하여 결정<br/>size: 오브젝트의 크기를 이용하여 결정<br/>time: 시간을 이용하여 결정 |
| 기준 시각 | 15 | number | 오브젝트를 분할할 기준이 될 시간을 설정합니다. | 오브젝트 로테이션 정책이 size\_and\_time 또는 time인 경우 설정 |
| 기준 오브젝트 크기 | 5242880 | number | 오브젝트를 분할할 기준이 될 크기를 설정합니다. | 오브젝트 로테이션 정책이 size\_and\_time 또는 size인 경우 설정 |
| ACL | private | enum | 오브젝트를 업로드했을 때 설정할 ACL 정책을 입력합니다. |  |
| 추가 설정 | { } | hash | S3에 연결하기 위한 추가 설정을 입력합니다. | [가이드](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html) |

### 출력 예제

* OBS와 동일합니다.

### Parquet 코덱 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| parquet 압축 코덱 | SNAPPY | enum | parquet 파일 변환 시 사용할 압축 코덱을 입력합니다. | [참조](https://parquet.apache.org/docs/file-format/data-pages/compression/) |

### 추가 설정 예시

#### follow redirects

* `true`로 설정하는 경우 AWS S3에서 307 응답을 리턴하는 경우 redirect 경로를 추적

``` js
{
    follow_redirects → true
}
```

#### retry limit

* 4xx, 5xx 응답에 대한 최대 재시도 횟수

``` js
{
    retry_limit → 5
}
```

#### force\_path\_style

* `true`일 경우 URL이 virtual-hosted-style이 아닌 path-style이어야 합니다. [참조](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/VirtualHosting.html)

``` js
{
    force_path_style → true
}
```

## Sink > (Apache) Kafka

### 노드 설명

* Kafka에 데이터를 전송하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 토픽 | - | string | 메시지를 전송할 Kafka 토픽 이름을 입력합니다. |  |
| 브로커 서버 목록 | localhost:9092 | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>예) 10.100.1.1:9092,10.100.1.2:9092 |
| 클라이언트 아이디 | dataflow | string | Kafka Producer를 식별하는 ID를 입력합니다. | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| 메시지 직렬화 유형 | org.apache.kafka.common.serialization.StringSerializer | string | 전송하는 메시지의 값을 직렬화할 방법을 입력합니다. | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| 압축 유형 | none | enum | 전송하는 데이터를 압축할 방법을 입력합니다. | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br/>none, gzip, snappy, lz4 중 선택 |
| 키 직렬화 유형 | org.apache.kafka.common.serialization.StringSerializer | string | 전송하는 메시지의 키를 직렬화할 방법을 입력합니다. | [key.serializer](https://kafka.apache.org/documentation/#producerconfigs_key.serializer) |
| 메타데이터 갱신 주기 | 300000 | number | 파티션, 브로커 서버 상태 등을 갱신할 주기(ms)를 입력합니다. | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms) |
| 최대 요청 크기 | 1048576 | number | 전송 요청당 최대 크기(byte)를 입력합니다. | [max.request.size](https://kafka.apache.org/documentation/#producerconfigs_max.request.size) |
| 서버 재연결 주기 | 50 | number | 브로커 서버에 연결이 실패했을 때 재시도할 주기를 입력합니다. | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms) |
| 배치 크기 | 16384 | number | 배치 요청으로 전송할 크기(byte)를 입력합니다. | [batch.size](https://kafka.apache.org/documentation/#producerconfigs_batch.size) |
| 버퍼 메모리 | 33554432 | number | Kafka 전송에 사용하는 버퍼의 크기(byte)를 입력합니다. | [buffer.memory](https://kafka.apache.org/documentation/#producerconfigs_buffer.memory) |
| 수신 버퍼 크기 | 32768 | number | 데이터를 읽는 데 사용하는 TCP receive 버퍼의 크기(byte)를 입력합니다. | [receive.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_receive.buffer.bytes) |
| 전송 지연 시간 | 0 | number | 메시지 전송을 지연할 시간을 입력합니다. 지연된 메시지는 배치 요청으로 한번에 전송합니다. | [linger.ms](https://kafka.apache.org/documentation/#producerconfigs_linger.ms) |
| 서버 요청 타임아웃 | 40000 | number | 전송 요청에 대한 타임아웃(ms)을 입력합니다. | [request.timeout.ms](https://kafka.apache.org/documentation/#producerconfigs_request.timeout.ms) |
| 메타데이터 조회 타임아웃 |  | number |  | [https://kafka.apache.org/documentation/#upgrade\_1100\_notable](https://kafka.apache.org/documentation/#upgrade_1100_notable) |
| 전송 버퍼 크기 | 131072 | number | 데이터를 전송하는 데 사용하는 TCP send 버퍼의 크기(byte)를 입력합니다. | [send.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_send.buffer.bytes) |
| ack 속성 | 1 | enum | 브로커 서버에서 메시지를 받았는지 확인하는 설정을 입력합니다. | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br/>0 - 메시지 수신 여부를 확인하지 않습니다.<br/>1 - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다리지 않고 메시지를 수신했다는 응답을 합니다.<br/>all - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다린 뒤 메시지를 수신했다는 응답을 합니다. |
| 요청 재연결 주기 | 100 | number | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms) |
| 재시도 횟수 | - | number | 전송 요청이 실패했을 때 재시도할 최대 횟수를 입력합니다. | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br/>설정값을 초과하여 재시도하는 경우 데이터 유실이 발생할 수 있습니다. |

### json 코덱 출력 예제

#### 입력 메시지

``` json
{
    "message": "Hello World!",
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 출력 메시지

``` json
{"host":"0bc501d89f8c","message":"Hello World","hidden":"Hello DataFlow!","sequence":0,"@timestamp":"2022-11-21T07:49:20.000Z","@version":"1"}
```

### plain 코덱 출력 예제

#### 입력 메시지

``` json
{
    "message": "Hello World!",
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 출력 메시지

```
2022-11-21T07:49:20.000Z 2898d492114d Hello World
```

### plain 코덱 출력 예제 - message 필드 존재하지 않을 경우

#### 입력 메시지

``` json
{
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 출력 메시지

```
2022-11-21T07:49:20.000Z e5ef7ece9bb0 %{message}
```

## Sink > Stdout

### 노드 설명

* 표준 출력으로 메시지를 출력하는 노드입니다.
* Source, Filter 노드에서 처리된 데이터를 확인할 때 유용하게 사용할 수 있습니다.

### 코덱별 출력 예제

#### 입력 메시지

``` json
{
    "host": "data-flow-01",
    "message": "Hello World!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### plain 코덱 출력 메시지

```
2022-11-21T07:49:20Z data-flow-01 Hello World!
```

#### json 코덱 출력 예제

```
{"host": "data-flow-01", "message": "Hello World!", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### line 코덱 출력 예제

* format은 다음과 같이 설정
  * `%{message} %{host}`

```
Hello World! data-flow-01
```

#### debug 코덱 출력 예제

```
{
        "host" => "data-flow-01",
     "message" => "Hello World!",
  "@timestamp" => 2022-11-21T07:49:20Z
}
```

## Branch

* 인입된 데이터의 값에 따라 흐름 분기를 정의하는 노드 유형입니다.

## IF

### 노드 설명

* 조건문을 통해 메시지를 필터링하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 조건문 | - | string | 메시지를 필터링할 조건을 입력합니다. |  |

### 필터링 예제 - first depth field reference

#### 조건

* 조건문 → `[logLevel] == "ERROR"`

#### 통과 메시지

``` json
{
       "logLevel": "ERROR"
}
```

#### 누락 메시지

``` json
{
   "logLevel": "INFO"
}
```

### 필터링 예제 - second depth field reference

#### 조건

* 조건문 → `[response][status] == 200`

#### 통과 메시지

``` json
{
    "resposne": {
        "status": 200
    }
}
```

#### 누락 메시지

``` json
{
    "resposne": {
        "status": 404
    }
}
```
