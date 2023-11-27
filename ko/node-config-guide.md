## Data & Analytics > DataFlow > 노드 설정 가이드

* 노드 유형은 손쉽게 플로우를 작성할 수 있게 선정의된 템플릿입니다.
* 노드 유형의 종류는 Source, Filter, Branch, Sink입니다.
* Source, Sink 노드 유형은 반드시 테스트를 수행하여 엔드포인트 정보가 유효한지 확인하기를 권장합니다.

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

## Source

* 플로우로 데이터를 인입할 엔드포인트를 정의하는 노드 유형입니다.

### Source 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 유형 | - | string | 각 메시지에 주어진 값으로 `type` 필드를 생성합니다. |  |
| 측정 항목 활성화 | true | boolean | 노드의 메트릭을 수집합니다.<br/>속성값이 true일 경우 모니터링 탭에서 노드의 이벤트 메트릭 정보를 확인할 수 있습니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |
| 태그 | - | array of string | 각 메시지에 주어진 값의 태그를 추가합니다. |  |
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
* Log & Crash Search의 [로그 검색 API](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/#api_1)의 토큰에 영향을 받습니다.
  * 토큰이 부족할 경우 Log & Crash Search로 문의하십시오.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Log & Crash Search의 앱키를 입력합니다. |  |
| SecretKey | - | string | Log & Crash Search의 시크릿키를 입력합니다. |  |
| 조회 시작 시간 | - | string | 로그 조회의 시작 시간을 입력합니다. | [참고](#dsl) |
| 조회 종료 시간 | - | string | 로그 조회의 종료 시간을 입력합니다. |  |

* 조회 시작 시간과 조회 종료 시간 설정
    * 조회 종료 시간이 플로우 실행 시점보다 늦더라도 플로우는 조회 종료 시간까지 대기하지 않고 현재 조회할 수 있는 데이터만 조회한 뒤 종료합니다.

### 코덱별 메시지 인입

* Log & Crash Search는 기본적으로 ```JSON``` 형식의 데이터를 다룹니다.
    * [참고 - Log & Crash Search API 가이드](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/)
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

## Source > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage로부터 데이터를 입력 받는 노드입니다.
* 오브젝트 생성 시간을 기준으로 가장 빨리 생성된 오브젝트부터 데이터를 읽습니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 버킷 | - | string | 데이터를 읽을 버킷 이름을 입력합니다. |  |
| 리전 | - | string | 저장소에 설정된 리전 정보를 입력합니다. |  |
| 비밀 키 | - | string | S3가 발급한 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | - | string | S3가 발급한 자격 증명 액세스 키를 입력합니다. |  |
| 리스트 갱신 주기 | - | number | 버킷에 포함된 오브젝트 리스트 갱신 주기를 입력합니다. |  |
| 새로운 파일 체크 여부 | - | boolean | 속성값이 true일 경우 새로 추가되는 오브젝트도 함께 읽어 옵니다. |  |
| Prefix | - | string | 읽어 올 오브젝트의 접두사를 입력합니다. |  |
| 제외할 키 패턴 | - | string | 읽지 않을 오브젝트의 패턴을 입력합니다. |  |
| 삭제 | false | boolean | 속성값이 true일 경우 읽기 완료한 오브젝트를 삭제합니다. |  |

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
| 메타정보 포함 여부 | - | boolean | S3 오브젝트의 메타데이터를 키로 포함할지 여부를 결정합니다. | (마지막 수정 시간, Content-Type 등) |
| Prefix | - | string | 읽어 올 오브젝트의 접두사를 입력합니다. |  |
| 제외할 키 패턴 | - | string | 읽지 않을 오브젝트의 패턴을 입력합니다. |  |
| 추가 설정 | - | hash | S3 서버와 연결할 때 사용할 추가적인 설정을 입력합니다. | 사용 가능한 설정의 전체 목록은 다음 링크를 참조하십시오.<br/>https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html<br/>예)<br/>{<br/>"force\_path\_style": true<br/>} |

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

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 브로커 서버 목록 | localhost:9092 | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [bootstrap.servers](https://kafka.apache.org/documentation/#consumerconfigs_bootstrap.servers)<br/>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| 컨슈머 그룹 아이디 | dataflow | string | Kafka Consumer Group을 식별하는 ID를 입력합니다. | [group.id](https://kafka.apache.org/documentation/#consumerconfigs_group.id) |
| 내부 토픽 제외 여부 | true | boolean |  | [exclude.internal.topics](https://kafka.apache.org/documentation/#consumerconfigs_exclude.internal.topics)<br/>수신 대상에서 `__consumer_offsets`와 같은 내부 토픽을 제외합니다. |
| 토픽 패턴 | - | string | 메시지를 수신할 Kafka 토픽 패턴을 입력합니다. | ex) `*-messages` |
| 클라이언트 아이디 | dataflow | string | Kafka Consumer를 식별하는 ID를 입력합니다. | [client.id](https://kafka.apache.org/documentation/#consumerconfigs_client.id) |
| 파티션 할당 정책 | - | string | Kafka에서 메시지 수신 시 컨슈머 그룹에 어떻게 파티션을 할당할지 결정합니다. | [partition.assignment.strategy](https://kafka.apache.org/documentation/#consumerconfigs_partition.assignment.strategy)<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| 오프셋 설정 | none | enum | 컨슈머 그룹의 오프셋을 설정하는 기준을 입력합니다. | [auto.offset.reset](https://kafka.apache.org/documentation/#consumerconfigs_auto.offset.reset)<br/>아래 설정 모두 컨슈머 그룹이 이미 존재하는 경우 기존 오프셋을 유지합니다.<br/>none: 컨슈머 그룹이 없으면 오류를 반환합니다.<br/>earliest: 컨슈머 그룹이 없으면 파티션의 가장 오래된 오프셋으로 초기화합니다.<br/>latest: 컨슈머 그룹이 없으면 파티션의 가장 최근 오프셋으로 초기화합니다. |
| 오프셋 커밋 주기 | 5000 | number | 컨슈머 그룹의 오프셋을 갱신할 주기를 입력합니다. | [auto.commit.internal.ms](https://kafka.apache.org/documentation/#consumerconfigs_auto.commit.interval.ms) |
| 오프셋 자동 커밋 여부 | true | boolean |  | [enable.auto.commit](https://kafka.apache.org/documentation/#consumerconfigs_enable.auto.commit) |
| 키 역직렬화 유형 | org.apache.kafka.common.serialization.StringDeserializer | string | 수신하는 메시지의 키를 직렬화할 방법을 입력합니다. | [key.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_key.deserializer) |
| 메시지 역직렬화 유형 | org.apache.kafka.common.serialization.StringDeserializer | string | 수신하는 메시지의 값을 직렬화할 방법을 입력합니다. | [value.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_value.deserializer) |
| 메타데이터 생성 여부 | false | boolean | 속성값이 true일 경우 메시지에 대한 메타데이터 필드를 생성합니다. 메타데이터 필드를 활용하기 위해서는 filter 노드 유형을 조합해야 합니다(하단 가이드 참조). | 생성되는 필드는 다음과 같습니다.<br/>topic: 메시지를 수신한 토픽<br/>consumer\_group: 메시지를 수신하는 데 사용한 컨슈머 그룹 아이디<br/>partition: 메시지를 수신한 토픽의 파티션 번호<br/>offset: 메시지를 수신한 파티션의 오프셋<br/>key: 메시지 키를 포함하는 ByteBuffer |
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

* `메타데이터 생성 여부` 설정 활성화 시 메타데이터 필드가 생성되나, 별도로 일반 필드로 주입하는 작업을 거치지 않는다면 Filter, Sink 등 플러그인에서 노출하지 않습니다.
* 설정 활성화 시 Kafka 플러그인 이후의 메시지 예시
```js
{
    // 일반 필드
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafka 토픽 메시지..."

    // 메타데이터 필드
    // 사용자가 일반 필드로 주입하기 전까지 활용할 수 없음
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

## Filter

* 인입된 데이터를 어떻게 처리할지 정의하는 노드 유형입니다.

### Filter 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 측정 항목 활성화 | true | boolean | 노드의 메트릭을 수집합니다.<br/>속성값이 true일 경우 모니터링 탭에서 노드의 이벤트 메트릭 정보를 확인할 수 있습니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |
| 태그 추가 | - | array of string | 각 메시지에 태그를 추가합니다. |  |
| 태그 삭제 | - | array of string | 각 메시지에 주어진 태그를 삭제합니다. |  |
| 필드 삭제 | - | array of string | 각 메시지의 필드를 삭제합니다. |  |
| 필드 추가 | - | hash | 커스텀 필드를 추가할 수 있습니다.<br/>`%{[depth1_field]}`로 각 필드의 값을 가져와 필드를 추가할 수 있습니다. |  |

## Filter > Alter

### 노드 설명

* 메시지 필드 값을 다른 값으로 변경합니다.
* 최상위 필드만 변경할 수 있습니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 필드 덮어쓰기 | - | array of strings | 필드 값을 주어진 값과 비교하여 같을 경우 다른 필드의 값을 주어진 값으로 수정합니다. |  |
| 필드 변경 | - | array of strings | 필드 값을 주어진 값과 비교하여 같을 경우 해당 필드의 값을 주어진 값으로 수정합니다. |  |
| Coalesce | - | array of strings | 하나의 필드에 뒤이어 오는 필드 중 처음으로 null이 아닌 값을 할당합니다. |  |

### 필드 덮어쓰기 예제

#### 조건

* 필드 덮어쓰기 → `["logType", "ERROR", "isBillingTarget", "false"]`

#### 입력 메시지

```js
{
    "logType": "ERROR"
}
```

#### 출력 메시지

```js
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
* 암호화 키는 SKM을 참조합니다.
    * SKM 키 등록에 대한 자세한 내용은 [SKM 가이드 문서](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/overview/)를 참고하십시오.
    * ```한 플로우에 여러 Cipher 노드가 포함되더라도 모든 Cipher 노드는 반드시 하나의 SKM 키 레퍼런스만 참조할 수 있습니다.```

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 모드 | - | enum | 암호화 모드와 복호화 모드 중 선택합니다. | 목록 중에 하나를 선택합니다. |
| 앱키 | - | string | 암/복호화에 사용할 키를 저장한 SKM 앱키를 입력합니다. |  |
| 키 ID | - | string | 암/복호화에 사용할 키를 저장한 SKM 키 ID를 입력합니다. |  |
| 키 버전 | - | string | 암/복호화에 사용할 키를 저장한 SKM 키 버전을 입력합니다. |  |
| 암/복호화 키 길이 | 16 | positive integer | 암/복호화 키의 길이를 입력합니다. |  |
| IV 랜덤 길이 | - | positive number | Initial Vector의 random bytes 길이를 입력합니다. |  |
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
| 패턴 정의 | - | hash | 파싱할 토큰의 규칙의 사용자 정의 패턴을 정규표현식으로 입력합니다. | 시스템 정의 패턴은 아래 링크를 확인하십시오.<br/>http://grokdebug.herokuapp.com/patterns |
| 실패 태그 | - | array of strings | 문자열 파싱에 실패할 경우 정의할 태그명을 입력합니다. |  |
| 타임아웃 | 30000 | numeric | 문자열 파싱이 될 때까지 기다리는 시간을 입력합니다. |  |
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
| Quote | " | string | 칼럼 필드를 나누는 문자를 입력합니다. |  |
| 첫 행 무시 여부 | false | boolean | 속성값이 true일 경우 읽은 데이터 중 첫 행에 입력된 칼럼 이름을 무시합니다. |  |
| 칼럼 | - | array of strings | 칼럼 이름을 입력합니다. |  |
| 구분자 | , | string | 칼럼을 구분할 문자열을 입력합니다. |  |
| 소스 필드 | message | string | CSV 파싱할 필드명을 입력합니다. |  |
| 스키마 | - | hash | 각 칼럼의 이름과 자료형을 dictionary 형태로 입력합니다. | 칼럼에 정의된 필드와 별개로 등록합니다.<br/>자료형은 기본적으로 string이며, 다른 자료형으로 변환이 필요할 경우 스키마 설정을 활용합니다.<br/>가능한 자료형은 다음과 같습니다.<br/>integer, float, date, date_time, boolean |

### 자료형 없는 CSV 파싱 예제

#### 조건

* 소스 필드 → `message`
* 칼럼 → `["one", "two", "t hree"]`

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
* 칼럼 → `["one", "two", "t hree"]`

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
* 칼럼 → `["one", "two", "t hree"]`
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
| 패턴 정의 | - | json | 파싱할 토큰의 규칙의 사용자 정의 패턴을 정규표현식으로 입력합니다. | 시스템 정의 패턴은 아래 링크를 확인하십시오.<br/>http://grokdebug.herokuapp.com/patterns |
| 실패 태그 | - | array of strings | 문자열 파싱에 실패할 경우 정의할 태그명을 입력합니다. |  |
| 타임아웃 | 30000 | numeric | 문자열 파싱이 될 때까지 기다리는 시간을 입력합니다. |  |
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
| Match | - | array of strings | 문자열을 가져오기 위한 필드명과 포맷을 입력합니다. |  |
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

## Sink

* Filter 작업을 마친 데이터를 적재할 엔드포인트를 정의하는 노드 유형입니다.

### Sink 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 측정 항목 활성화 | true | boolean | 노드의 메트릭을 수집합니다.<br/>속성값이 true일 경우 모니터링 탭에서 노드의 이벤트 메트릭 정보를 확인할 수 있습니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |

## Sink > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage에 데이터를 업로드하는 노드입니다.
* OBS에 작성되는 Object는 기본적으로 다음 경로 포맷에 맞게 출력됩니다.
    * `/{container_name}/{yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 리전 | - | enum | Object Storage 상품의 리전을 입력합니다. | [OBS 리전 상세](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#aws-sdk) |
| 버킷 | - | string | 버킷 이름을 입력합니다. |  |
| 비밀 키 | - | string | S3 API 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | - | string | S3 API 자격 증명 액세스 키를 입력합니다. |  |
| Prefix | /%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | 파일 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix 시간 필드 | @timestamp | string | Prefix에 적용할 시간 필드를 입력합니다. |  |
| Prefix 시간 필드 타입 | DATE_FILTER_RESULT | enum | Prefix에 적용할 시간 필드의 타입을 입력합니다. |  |
| Prefix 시간대 | UTC | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다. |  |
| Prefix 시간 적용 fallback  | _prefix_datetime_parse_failure | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다. |  |
| 인코딩 | none | enum | 인코딩 여부를 입력합니다. gzip 인코딩을 사용할 수 있습니다. |  |
| 파일 로테이션 정책 | size\_and\_time | enum | 파일의 생성 규칙을 결정합니다. | size\_and\_time - 파일의 크기와 시간을 이용하여 결정<br/>size - 파일의 크기를 이용하여 결정<br/>time - 시간을 이용하여 결정 |
| 기준 시각 | 15 | number | 파일을 분할할 기준이 될 시간을 설정합니다. | 파일 로테이션 정책이 size\_and\_time 또는 time인 경우 설정 |
| 파일 크기 | 5242880 | number | 파일을 분할할 기준이 될 크기를 설정합니다. | 파일 로테이션 정책이 size\_and\_time 또는 size인 경우 설정 |
| ACL | private | enum | 파일을 업로드할 때 설정할 ACL 정책을 입력합니다. | 
| 스토리지 클래스 | STANDARD | enum | 파일을 업로드할 때 사용할 스토리지 클래스를 설정합니다. | [스토리지 클래스 가이드l](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |

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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* 출력 메시지

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
```

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
| Prefix | - | string | 파일 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix 시간 필드 | @timestamp | string | Prefix에 적용할 시간 필드를 입력합니다. |  |
| Prefix 시간 필드 타입 | DATE_FILTER_RESULT | enum | Prefix에 적용할 시간 필드의 타입을 입력합니다. |  |
| Prefix 시간대 | UTC | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다. |  |
| Prefix 시간 적용 fallback  | _prefix_datetime_parse_failure | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다. |  |
| 스토리지 클래스 | STANDARD | enum | 파일을 업로드할 때 사용할 스토리지 클래스를 설정합니다. | [스토리지 클래스 가이드](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| 인코딩 | none | enum | 인코딩 여부를 입력합니다. gzip 인코딩을 사용할 수 있습니다. |  |
| 파일 로테이션 정책 | size\_and\_time | enum | 파일의 생성 규칙을 결정합니다. | size\_and\_time - 파일의 크기와 시간을 이용하여 결정<br/>size - 파일의 크기를 이용하여 결정<br/>time - 시간을 이용하여 결정 |
| 기준 시각 | 15 | number | 파일을 분할할 기준이 될 시간을 설정합니다. | 파일 로테이션 정책이 size\_and\_time 또는 time인 경우 설정 |
| 파일 크기 | 5242880 | number | 파일을 분할할 기준이 될 크기를 설정합니다. | 파일 로테이션 정책이 size\_and\_time 또는 size인 경우 설정 |
| ACL | private | enum | 파일을 업로드했을 때 설정할 ACL 정책을 입력합니다. |  |
| 추가 설정 | { } | hash | S3에 연결하기 위한 추가 설정을 입력합니다. | [가이드](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html) |

### 출력 예제

* OBS와 동일합니다.

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
| 브로커 서버 목록 | localhost:9092 | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>ex) 10.100.1.1:9092,10.100.1.2:9092 |
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
