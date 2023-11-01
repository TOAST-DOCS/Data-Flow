## Data & Analytics > DataFlow > 에러 코드 가이드

| 에러 코드                   | 설명                                                          | 해결 방법                                                  |
|-------------------------|-------------------------------------------------------------|--------------------------------------------------------|
| FLOW_GRAPH_DISCONNECTED | 플로우가 connected graph가 아닙니다.                                 | 플로우를 connected graph로 구성하십시오.                          |
| FLOW_GRAPH_CYCLE        | 플로우가 cycle이 있는 그래프입니다.                                      | 플로우를 트리로 변경하십시오.                                       |
| FLOW_NODE_DUPLICATED    | 플로우에 중복된 node ID가 있습니다.                                     | 각 노드의 ID를 다르게 변경하십시오.                                  |
| FLOW_INCOMPLETE         | 노드가 없거나 노드를 연결하는 링크가 없습니다.                                  | 각 노드 생성 후 노드를 연결하십시오.                                  |
| FLOW_INVALID_PROPERTY   | 노드의 property가 올바르지 않습니다.                                    | 각 노드의 property를 확인하십시오.                                |
| FLOW_INVALID_DSL        | 노드의 DSL이 올바르지 않습니다.                                         | 각 노드의 DSL을 확인하십시오.                                     |
| SKM_INVALID_APPKEY      | Cipher 노드의 appkey 정보가 유효하지 않습니다.                            | Secure Key Manager appkey를 확인하십시오.                     |
| SKM_INVALID_KEY_ID      | Cipher 노드의 key ID 정보가 유효하지 않습니다.                            | Secure Key Manager key ID를 확인하십시오.                     |
| SKM_INVALID_KEY_VERSION | Cipher 노드의 key 버전 정보가 유효하지 않습니다.                            | Secure Key Manager key 버전을 확인하십시오.                     |
| LNCS_UNAUTHENTICATED    | Log & Crash Search 노드의 appkey, secretkey가 유효하지 않습니다.        | Log & Crash Search appkey, secretkey를 확인하십시오.          |
| LNCS_SEARCH_LIMIT       | Log & Crash Search 노드가 검색 제한에 도달했습니다.                       | 고객 센터로 문의하십시오.                                         |
| LNCS_SERVICE_UNSTABLE   | Log & Crash Search 서비스가 불안정한 상태입니다.                         | 고객 센터로 문의하십시오.                                         |
| KAFKA_ENDPOINT_INVALID  | Kafka endpoint에 접근할 수 없습니다.                                 | Kafka endpoint 정보를 확인하십시오.                             |
| S3_UNAUTHENTICATED      | S3, Object Storage 노드의 access key 또는 secret key가 유효하지 않습니다. | S3, Object Storage 노드의 access key, secret key를 확인하십시오. |
| S3_ACCESS_DENIED        | S3, Object Storage 저장소에 접근이 거부되었습니다.                        | S3, Object Storage 노드의 access key에 부여된 ACL을 확인하십시오.    |
| S3_NO_SUCH_BUCKET       | S3, Object Storage 저장소에 버킷이 존재하지 않습니다.                      | S3, Object Storage 노드의 버킷을 확인하십시오.                     |
| S3_SERVICE_ERROR        | S3, Object Storage 저장소가 불안정한 상태입니다.                         | 해당 저장소로 문의하십시오.                                        |
| ERROR                   | 서비스 내부 오류 또는 정의되지 않은 에러입니다.                                 | 고객 센터로 문의하십시오.                                         |
