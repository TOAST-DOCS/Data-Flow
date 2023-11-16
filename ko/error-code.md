## Data & Analytics > DataFlow > 오류 코드 가이드

| 오류 코드                   | 설명                                                                                       |
|-------------------------|------------------------------------------------------------------------------------------|
| FLOW_GRAPH_DISCONNECTED | 플로우가 그래프가 끊어져 있습니다.                                                                      |  
| FLOW_GRAPH_CYCLE        | 플로우에 정의된 노드의 연결선(흐름)은 이전 노드로 되돌아갈 수 없습니다.                                                |
| FLOW_NODE_DUPLICATED    | 플로우에 중복된 node ID가 있습니다.                                                                  |
| FLOW_INCOMPLETE         | 노드가 없거나 노드를 연결하는 링크가 없습니다. 각 노드 생성 후 노드를 연결하십시오.                                         |
| FLOW_INVALID_PROPERTY   | 노드의 property가 올바르지 않습니다.                                                                 | 
| FLOW_INVALID_DSL        | 노드의 DSL이 올바르지 않습니다.                                                                      | 
| SKM_INVALID_APPKEY      | Cipher 노드의 appkey 정보가 유효하지 않습니다.                                                         |
| SKM_INVALID_KEY_ID      | Cipher 노드의 key ID 정보가 유효하지 않습니다.                                                         |
| SKM_INVALID_KEY_VERSION | Cipher 노드의 key 버전 정보가 유효하지 않습니다.                                                         |
| LNCS_UNAUTHENTICATED    | Log & Crash Search 노드의 appkey, secretkey가 유효하지 않습니다.                                     |
| LNCS_SEARCH_LIMIT       | Log & Crash Search 노드가 검색 제한에 도달했습니다.    고객 센터로 문의하십시오.                                  |
| LNCS_SERVICE_UNSTABLE   | Log & Crash Search 서비스가 불안정한 상태입니다.   고객 센터로 문의하십시오.                                     |
| KAFKA_ENDPOINT_INVALID  | Kafka endpoint에 접근할 수 없습니다.                                                              |
| S3_UNAUTHENTICATED      | S3, Object Storage 노드의 access key 또는 secret key가 유효하지 않습니다.                              |
| S3_ACCESS_DENIED        | S3, Object Storage 저장소에 접근이 거부되었습니다. S3, Object Storage 노드의 access key에 부여된 ACL을 확인하십시오. |
| S3_NO_SUCH_BUCKET       | S3, Object Storage 저장소에 버킷이 존재하지 않습니다.                                                   |
| S3_SERVICE_ERROR        | S3, Object Storage 저장소가 불안정한 상태입니다. 해당 저장소로 문의하십시오.                                      |
| ERROR                   | 서비스 내부 오류 또는 정의되지 않은 오류입니다. 고객 센터로 문의하십시오.                                               |
