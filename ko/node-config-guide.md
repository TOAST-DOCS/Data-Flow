## Data & Analytics > DataFlow > 노드 유형 가이드

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
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 마지막 시간을 리턴합니다.
    * [주의] 한국 시간을 기준으로 계산합니다.
    * ex\) \{\{ executionTime \| endOf: MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 `delta`만큼 뺀 시간을 리턴합니다.
    * ex\) \{\{ executionTime \| subTime: 10\, MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| subTime: 10\, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 `delta`만큼 더한 시간을 리턴합니다.
    * ex\) \{\{ executionTime \| addTime: 10\, MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| addTime: 10\, MINUTE \}\}
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
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| format: 'yyyy' \}\}
        * → 2022
* nested filter 예제
    * 플로우 실행이 시작된 날의 03시의 DSL 표현
        * =\> \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## Source

* 플로우로 데이터를 인입할 엔드포인트를 정의하는 노드 유형입니다.

### Source 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 유형 | - | string | 각 메시지에 주어진 값으로 `type` 필드를 생성합니다. |  |
| 측정 항목 활성화 | true | boolean | 노드의 메트릭을 수집합니다.<br>속성값이 true일 경우 모니터링 탭에서 노드의 이벤트 메트릭 정보를 확인할 수 있습니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |
| 태그 | - | array of string | 각 메시지에 주어진 값의 태그를 추가합니다. |  |
| 필드 추가 | - | hash | 커스텀 필드를 추가할 수 있습니다.<br>`%{[depth1_field]}`로 각 필드의 값을 가져와 필드를 추가할 수 있습니다. |  |

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
| 조회 시작 시간 | - | string | 로그 조회의 시작 시간을 입력합니다. | [참고](#dsl) |
| 조회 종료 시간 | - | string | 로그 조회의 종료 시간을 입력합니다. |  |

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

## Source > (NHN Cloud) CloudTrail

### 노드 설명

* (NHN Cloud) CloudTrail은 CloudTrail로부터 데이터를 읽어 오는 노드입니다.
* 노드에 데이터 조회 시작 시간을 설정할 수 있습니다. 설정하지 않으면 플로우를 시작하는 시점부터 데이터를 읽어 옵니다.
* 노드에 종료 시간을 입력하지 않으면 스트리밍 형식으로 데이터를 읽어 옵니다. 종료 시간을 입력하면 종료 시간까지의 데이터를 읽어 오고 플로우는 종료됩니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | CloudTrail의 앱키를 입력합니다. |  |
| 조회 시작 시간 | - | string | 데이터 조회의 시작 시간을 입력합니다. | [참고](#dsl) |
| 조회 종료 시간 | - | string | 데이터 조회의 종료 시간을 입력합니다. |  |

### 코덱별 메시지 인입

* CloudTrail은 기본적으로 ```JSON``` 형식의 데이터를 다루고 있습니다.
    * [참고 -CloudTrail API 가이드](https://docs.toast.com/ko/CloudTrail/ko/api-guide/)
* 코덱을 선택하지 않거나 plain인 경우 CloudTrail 데이터에 대한 JSON 문자열을 `message`라는 필드로 포함하게 됩니다.
* CloudTrail 데이터의 각 필드를 활용하고 싶다면 json 코덱을 사용하는 것이 좋습니다.

#### 미선택 혹은 plain

``` js
{
    "message":"{\\\"log\\\":\\\"CloudTrail\\\", \\\"Result\\\": \\\"Data\\\"}"
}
```

#### json

``` js
{"log":"CloudTrail", "Result": "Data"}
```

### Filter

* 인입된 데이터를 어떻게 처리할지 정의하는 노드 유형입니다.

### Filter 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 측정 항목 활성화 | true | boolean | 노드의 메트릭을 수집합니다.<br>속성값이 true일 경우 모니터링 탭에서 노드의 이벤트 메트릭 정보를 확인할 수 있습니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다<br>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |
| 태그 추가 | - | array of string | 각 메시지에 태그를 추가합니다. |  |
| 태그 삭제 | - | array of string | 각 메시지에 주어진 태그를 삭제합니다. |  |
| 필드 삭제 | - | array of string | 각 메시지의 필드를 삭제합니다. |  |
| 필드 추가 | - | hash | 커스텀 필드를 추가할 수 있습니다.<br>`%{[depth1_field]}`로 각 필드의 값을 가져와 필드를 추가할 수 있습니다. |  |

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

## Sink

* Filter 작업을 마친 데이터를 적재할 엔드포인트를 정의하는 노드 유형입니다.

### Sink 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 측정 항목 활성화 | true | boolean | 노드의 메트릭을 수집합니다.<br>속성값이 true일 경우 모니터링 탭에서 노드의 이벤트 메트릭 정보를 확인할 수 있습니다. |  |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |

## Sink > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage에 데이터를 업로드하는 노드입니다.
* OBS에 작성되는 Object는 다음 경로 포맷에 맞게 출력됩니다.
    * `/{container_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 리전 | - | enum | Object Storage 상품의 리전을 입력합니다. | [OBS 리전 상세](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#aws-sdk) |
| 버킷 | - | string | 버킷 이름을 입력합니다. |  |
| 비밀 키 | - | string | S3 API 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | - | string | S3 API 자격 증명 액세스 키를 입력합니다. |  |
| 인코딩 | none | enum | 인코딩 여부를 입력합니다. gzip 인코딩을 사용할 수 있습니다. |  |
| 파일 로테이션 정책 | size\_and\_time | enum | 파일의 생성 규칙을 결정합니다. | size\_and\_time - 파일의 크기와 시간을 이용하여 결정<br>size - 파일의 크기를 이용하여 결정<br>time - 시간을 이용하여 결정 |
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
| Prefix | - | string | 파일 업로드 시 이름 앞에 붙일 접두사를 입력합니다. |  |
| 스토리지 클래스 | STANDARD | enum | 파일을 업로드할 때 사용할 스토리지 클래스를 설정합니다. | [스토리지 클래스 가이드](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| 인코딩 | none | enum | 인코딩 여부를 입력합니다. gzip 인코딩을 사용할 수 있습니다. |  |
| 파일 로테이션 정책 | size\_and\_time | enum | 파일의 생성 규칙을 결정합니다. | size\_and\_time - 파일의 크기와 시간을 이용하여 결정<br>size - 파일의 크기를 이용하여 결정<br>time - 시간을 이용하여 결정 |
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
| 브로커 서버 목록 | localhost:9092 | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| 클라이언트 아이디 | dataflow | string | Kafka Producer를 식별하는 ID를 입력합니다. | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| 메시지 직렬화 유형 | org.apache.kafka.common.serialization.StringSerializer | string | 전송하는 메시지의 값을 직렬화할 방법을 입력합니다. | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| 압축 유형 | none | enum | 전송하는 데이터를 압축할 방법을 입력합니다. | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br>none, gzip, snappy, lz4 중 선택 |
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
| ack 속성 | 1 | enum | 브로커 서버에서 메시지를 받았는지 확인하는 설정을 입력합니다. | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br>0 - 메시지 수신 여부를 확인하지 않습니다.<br>1 - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다리지 않고 메시지를 수신했다는 응답을 합니다.<br>all - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다린 뒤 메시지를 수신했다는 응답을 합니다. |
| 요청 재연결 주기 | 100 | number | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms) |
| 재시도 횟수 | - | number | 전송 요청이 실패했을 때 재시도할 최대 횟수를 입력합니다. | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br>설정값을 초과하여 재시도하는 경우 데이터 유실이 발생할 수 있습니다. |

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
