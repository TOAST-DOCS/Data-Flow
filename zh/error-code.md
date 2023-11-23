## Data & Analytics > DataFlow > Error Code Guide

| Error Code                   | Description                                                                                       |
|-------------------------|------------------------------------------------------------------------------------------|
| FLOW_GRAPH_DISCONNECTED | The flow graph is disconnected.                                                                      |  
| FLOW_GRAPH_CYCLE        | A connection line (cycle) from a node defined in a flow cannot go back to a previous node.                                                |
| FLOW_NODE_DUPLICATED    | There are duplicate node IDs in the flow.                                                                  |
| FLOW_INCOMPLETE         | There are no nodes or links connecting the nodes. Connect the nodes after each node is created.                                         |
| FLOW_INVALID_PROPERTY   | The node's property is invalid.                                                                 | 
| FLOW_INVALID_DSL        | The DSL on the node is invalid.                                                                      | 
| SKM_INVALID_APPKEY      | The appkey information for the Cipher node is invalid.                                                         |
| SKM_INVALID_KEY_ID      | The key ID information for the Cipher node is invalid.                                                         |
| SKM_INVALID_KEY_VERSION | The key version information for the Cipher node is invalid.                                                         |
| LNCS_UNAUTHENTICATED    | The appkey, secretkey for the Log & Crash Search node is invalid.                                     |
| LNCS_SEARCH_LIMIT       | The Log & Crash Search node has reached its search limit. Please contact the Customer Center.                                  |
| LNCS_SERVICE_UNSTABLE   | The Log & Crash Search service is in an unstable state. Please contact the Customer Center.                                     |
| KAFKA_ENDPOINT_INVALID  | Cannot access the Kafka endpoint.                                                              |
| S3_UNAUTHENTICATED      | The access key or secret key for the S3, Object Storage node is invalid.                              |
| S3_ACCESS_DENIED        | Access to the S3, Object Storage store is denied. Check the ACL granted to the access key on the S3, Object Storage node. |
| S3_NO_SUCH_BUCKET       | The bucket does not exist in the S3, Object Storage store.                                                   |
| S3_SERVICE_ERROR        | The S3, Object Storage store is in an unstable state. Please contact the store.                                      |
| ERROR                   | Service internal error or undefined error. Please contact the Customer Center.                                               |
