### 2023. 01. 06

#### 버그 수정

* 플로우 편집 화면에서 노드를 추가한 후 첫 버튼 클릭이 동작하지 않는 문제를 수정했습니다.
* Cipher 노드의 필드 추가를 설정하더라도 실제로는 필드가 추가되지 않는 문제를 수정했습니다.

### 2022. 12. 27.

#### 신규 서비스 출시

* ETL 플로우 생성 및 실행이 가능한 서비스 입니다.
* 아래 나열된 Source를 지원합니다.
    * NHN Cloud Log & Crash Search
    * NHN Cloud CloudTrail
* 아래 나열된 Filter를 지원합니다.
    * Cipher (NHN Cloud Secure Key Manager 연동 필요)
* 아래 나열된 Sink를 지원합니다.
    * NHN Cloud Object Storage
    * Amazon S3 (Compatible)
    * Apache Kafka