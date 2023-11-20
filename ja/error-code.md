## Data & Analytics > DataFlow > エラーコードガイド

| エラーコード                  | 説明                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------|
| FLOW_GRAPH_DISCONNECTED | フローのグラフが切断されています。                                                                       |  
| FLOW_GRAPH_CYCLE        | フローに定義されたノードの接続線(フロー)は、前のノードに戻ることができません。                                                |
| FLOW_NODE_DUPLICATED    | フローに重複したnode IDがあります。                                                                  |
| FLOW_INCOMPLETE         | ノードがないか、ノードを接続するリンクがありません。 各ノードを作成した後、ノードを接続してください。                                         |
| FLOW_INVALID_PROPERTY   | ノードのpropertyが正しくありません。                                                                 | 
| FLOW_INVALID_DSL        | ノードのDSLが正しくありません。                                                                      | 
| SKM_INVALID_APPKEY      | Cipherノードのappkey情報が有効ではありません。                                                         |
| SKM_INVALID_KEY_ID      | Cipherノードのkey ID情報が有効ではありません。                                                         |
| SKM_INVALID_KEY_VERSION | Cipherノードのkeyバージョン情報が有効ではありません。                                                         |
| LNCS_UNAUTHENTICATED    | Log & Crash Searchノードのappkey、secretkeyが有効ではありません。                                     |
| LNCS_SEARCH_LIMIT       | Log & Crash Searchノードが検索制限に達しました。サポートにお問い合わせください。                                  |
| LNCS_SERVICE_UNSTABLE   | Log & Crash Searchサービスが不安定な状態です。サポートにお問い合わせください。                                     |
| KAFKA_ENDPOINT_INVALID  | Kafka endpointにアクセスできません。                                                              |
| S3_UNAUTHENTICATED      | S3, Object Storageノードのaccess keyまたはsecret keyが有効ではありません。                              |
| S3_ACCESS_DENIED        | S3、Object Storageストレージへのアクセスが拒否されました。 S3、Object Storageノードのaccess keyに付与されたACLをご確認ください。 |
| S3_NO_SUCH_BUCKET       | S3, Object Storageストレージにバケットが存在しません。                                                   |
| S3_SERVICE_ERROR        | S3, Object Storageストレージが不安定な状態です。該当ストレージにお問い合わせください。                                      |
| ERROR                   | サービス内部エラーまたは定義されていないエラーです。サポートにお問い合わせください。                                               |
