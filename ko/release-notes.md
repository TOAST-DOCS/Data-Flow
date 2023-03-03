## Data & Analytics > DataFlow > 릴리스 노트

### 2023. 03. 28.

#### 기능 추가

* 신규 노드 추가
    * Filter
        * Alter, Date, UUID, Split, Truncate 등 다양한 데이터 처리 방식을 추가했습니다.
* 플로우 사용량 노출 기능 추가
    * 콘솔에서 플로우 사용량을 실시간으로 확인할 수 있도록 기능을 추가했습니다.

#### 버그 수정

* PREPARING 단계로 진입하기 전에 플로우 사용량이 먼저 올라가는 버그를 수정했습니다.

### 2023. 02. 28.

#### 기능 추가

* 신규 노드 추가
    * Source
        * NHN Cloud ObjectStorage 노드로부터 데이터를 가져오는 기능을 추가했습니다.
        * Amazon S3 인터페이스를 통해 데이터를 가져오는 기능을 추가했습니다.
        * Apache Kafka를 통해 데이터를 가져오는 기능을 추가했습니다.
    * Filter
        * grok, json, csv 노드를 추가하여 데이터를 다양하게 전처리하는 기능을 추가했습니다.

### 2023. 01. 06.

#### 버그 수정

* 플로우 편집 화면에서 노드를 추가한 후 첫 버튼 클릭이 동작하지 않는 문제를 수정했습니다.
* Cipher 노드의 필드 추가를 설정해도 실제로는 필드가 추가되지 않는 문제를 수정했습니다.

### 2022. 12. 27.

#### 신규 서비스 출시

* ETL 플로우 생성 및 실행이 가능한 서비스입니다.
* 아래 나열된 Source를 지원합니다.
    * NHN Cloud Log & Crash Search
    * NHN Cloud CloudTrail
* 아래 나열된 Filter를 지원합니다.
    * Cipher (NHN Cloud Secure Key Manager 연동 필요)
* 아래 나열된 Sink를 지원합니다.
    * NHN Cloud Object Storage
    * Amazon S3 (Compatible)
    * Apache Kafka
