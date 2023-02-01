## Data & Analytics > DataFlow > Node Type Guide

* Node types are pre-defined templates for easy flow creation.
* Types of node types are Source, Filter, Branch, and Sink.
* It is recommended that Source, Sink Node type have to be tested to ensure that Endpoint information is valid.

## Domain Specific Language(DSL) Definition 

* DSL definition is required to execute flow.

### Variable

* `{{ executionTime }}`
    * Flow execution time
* Time unit
    * MINUTE - `{{ MINUTE }}`
    * HOUR - `{{ HOUR }}`
    * DAY - `{{ DAY }}`
    * MONTH - `{{ MONTH }}`
    * YEAR - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * Returns the start time of time zone defined by `unit` from the given time.
    * **Calculate based on Korean time.**
    * ex) {{ executionTime | startOf: MINUTE }}
    * ex) {{ 2022-11-04T13:31:28Z | startOf: MINUTE }}
        * => 2022-11-04T13:31:00Z
    * ex) {{ 2022-11-04T13:31:28Z | startOf: HOUR }}
        * => 2022-11-04T13:00:00Z
    * ex) {{ 2022-11-04T13:31:28Z | startOf: DAY }}
        * => 2022-11-04T00:00:00Z
    * ex) {{ 2022-11-04T13:31:28Z | startOf: MONTH }}
        * => 2022-11-01T00:00:00Z
    * ex) {{ 2022-11-04T13:31:28Z | startOf: YEAR }}
        * => 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * Returns the last time of time zone defined by `unit` from the given time.
    * **Calculate based on Korean time.**
    * ex) {{ executionTime | endOf: MINUTE }}
    * ex) {{ 2022-11-04T13:31:28Z | endOf: MINUTE }}
        * => 2022-11-04T13:31:59.999999999Z
    * ex) {{ 2022-11-04T13:31:28Z | endOf: HOUR }}
        * => 2022-11-04T13:59:59.999999999Z
    * ex) {{ 2022-11-04T13:31:28Z | endOf: DAY }}
        * => 2022-11-04T23:59:59.999999999Z
    * ex) {{ 2022-11-04T13:31:28Z | endOf: MONTH }}
        * => 2022-11-30T23:59:59.999999999Z
    * ex) {{ 2022-11-04T13:31:28Z | endOf: YEAR }}
        * => 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * Returns the time subtracted by `delta` in the time zone defined by `unit` from the given time.
    * ex) {{ executionTime | subTime: 10, MINUTE }}
    * ex) {{ 2022-11-04T13:31:28Z | subTime: 10, MINUTE }}
        * => 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * Returns the time added by `delta` in the time zone defined by `unit` from the given time.
    * ex) {{ executionTime | addTime: 10, MINUTE }}
    * ex) {{ 2022-11-04T13:31:28Z | addTime: 10, MINUTE }}
        * => 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * Returns the given time in the form `formatStr`.
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
    * ex) {{ executionTime | format: 'yyyy' }}
    * ex) {{ 2022-11-04T13:31:28Z | format: 'yyyy' }}
        * => 2022
* nested filter example
    * DSL expression at 03:00 hour on the day the flow started
        * => {{ executionTime | startOf: DAY | addTime: 3, HOUR }}

## Source

* Node type that defines an endpoint that imports data to the flow.

## Common Settings on Source Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Type | - | string | Create `type` field with the value given in each message. |  |
| Activate Measurement Items  | true | boolean | Collect metrics for nodes.<br>If Property value is true, you can check the event metric information for node in the Monitoring tab. |  |
| ID | - | string | Sets Node ID<br>Mark the Node name on the chart board with values defined in this property. |  |
| Tag | - | array of string | Add the tag of given value to each message. |  |
| Add Field | - | Hash | You can add a custom field<br>You can add fields by calling in the value of each field with `%{[depth1_field]}`. |  |

## Example of adding fields

``` json
{ 
    "my_custom_field": "%{[json_body][logType]}" 
}
```

## (NHN Cloud) Log&Crash Search

### Node Description

* (NHN Cloud) Log&Crash Search Node is node that reads logs from Log&Crash Search.
* You can set the log query start time for a node. If not set, the log is read from the start of the flow.
* If no end time is entered in the node, logs are read in streaming format. If an end time is entered, logs are read up to the end time and the flow ends.
* ```Currently, session logs and crash logs are not supported.```
* Affected by tokens from Log&Crash Search's [Log Search API](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/#api_1).
  * If you don't have enough tokens, you need to contact Log&Crash Search.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Enter the app key for Log&Crash Search. |  |
| Query Start time | - | string | Enter the start time of log query. | [Note](#dsl) |
| Query End time | - | string | Enter the end time of log query. |  |

* Set the query start and end time
    * Even if the query end time is later than the flow execution time, the flow does not wait until the query end time and ends after querying only the currently available data.


### Message imported by codec

* Log&Crash Search covers data in format **JSON** by default.
    * [Note - Log&Crash Search API Guide](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/)
* If no codec is selected or Plain, JSON string for the Log&Crash Search Log will be included in the field `message`.
* If you want to use each field in Log&Crash Search log, we recommend using json Codec.

#### Not selected or plain

``` js
{ 
    "message":"{\\\"log\\\":\\\"&\\\", \\\"Crash\\\": \\\"Search\\\", \\\"Result\\\": \\\"Data\\\"}" 
}
```

#### json

``` js
{"log":"&", "Crash": "Search", "Result": "Data"}
```

## (NHN Cloud) CloudTrail

### Node Description

* (NHN Cloud) CloudTrail is a node that reads data from CloudTrail.
* You can set the data query start time for a node. If not set, data is read from the start of the flow.
* If no end time is entered in the node, data is read in streaming format. If an end time is entered, the data up to the end time is read and the flow ends.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Enter the app key for CloudTrail. |  |
| Query Start time | - | string | Enter the start time of data Query. | [Note](#dsl) |
| Log End time | - | string | Enter the end time of data Query. |  |

* Set the query start and end time
    * Even if the query end time is later than the flow execution time, the flow does not wait until the query end time and ends after querying only the currently available data.

### Message imported by codec

* CloudTrail covers data in the format **JSON** by default.
    * [Note - CloudTrail API Guide](https://docs.toast.com/ko/CloudTrail/ko/api-guide/)
* If no codec is selected or Plain, JSON string for CloudTrail data will be included as field called `message`.
* If you want to use each field in CloudTrail data, we recommend using json Codec.

#### Not selected or Plain

``` js
{ 
    "message":"{\\\"log\\\":\\\"CloudTrail\\\", \\\"Result\\\": \\\"Data\\\"}" 
}
```

#### json

``` js
{"log":"CloudTrail", "Result": "Data"}
```

## Filter

* Node type that defines how to handle imported data.

## Common Settings on Filter Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Activate Measurement Items  | true | boolean | Collect metrics for nodes.<br>If Property value is true, you can check the event metric information for node in the Monitoring tab. |  |
| ID | - | string | Sets Node ID<br>Mark node name on chart board with values defined in this property. |  |
| Add Tag | - | array of string | Add Tag of each message |  |
| Delete Tag | - | array of string | Delete Tag that was given to each message |  |
| Delete Field | - | array of string | Delete Field of each message  |  |
| Add Field | - | Hash | You can add a custom field<br>You can add fields by calling in the value of each Field with `%{[depth1_field]}`. |  |

## Cipher

### Node Description

* Node for decrypting message field values.
* Encryption key refers to the SKM.
    * For more information on registering SKM keys, refer to [SKM Guide Document](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/overview/).
    * ```Even if flow contains multiple Cipher Nodes, all Cipher nodes must refer to only one SKM key reference.```

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Mode | - | enum | Choose between encryption mode and decryption mode. | Select one from the list. |
| Appkey | - | string | Enter SKM app key that saves the key for encryption/decryption. |  |
| Key ID | - | string | Enter SKM ID that saves the key for encryption/decryption. |  |
| Key Version | - | string | Enter SKM key version that saves the key for encryption/decryption. |  |
| Encryption/decryption key length | 16 | positive integer | Enter encryption/decryption key length |  |
| IV Random Length | - | positive number | Enter random bytes length of Initial Vector.  |  |
| Source Field | - | string | Enter Field name for encryption/decryption. |  |
| Field to be stored | - | string | Enter Field name to save encryption/decryption result. |  |

### Encrypt example exercise 

#### condition

* mode => `encrypt`
* Appkey => `SKM appkey`
* Key ID => `SKM Symmetric key ID`
* Key Version => `1`
* IV Random Length => `16`
* Source Field => message
* Field to be  stored => encrypted_message

#### Input message

``` js
{ 
    "message": "this is plain message" 
}
```

#### Output message

``` js
{ 
    "message": "this is plain message", 
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e" 
}
```

### decrypt example

#### condition

* mode => `decrypt`
* Appkey => `SKM appkey`
* Key ID => `SKM Symmetric key ID`
* Key Version => `1`
* IV Random Length => `16`
* Source Field => message
* Field to be  stored => decrypted_message

#### Input message

``` js
{ 
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e" 
}
```

#### Output message

``` js
{ 
    "decrypted_message": "this is plain message", 
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e" 
}
```

## Sink

* Type of node that defines an endpoint to load data that has completed filter operation.

## Common Settings on Sink Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Activate Measurement Items  | true | boolean | Collect metrics for nodes.<br>If property value is true, you can check the event metric information for node in the monitoring tab. |  |
| ID | - | string | Sets Node ID<br>Mark node name on the chart board with values defined in this property. |  |

## (NHN Cloud) Object Storage

### Node Description

* Node for uploading data to Object Storage in NHN Cloud.
* Object created on OBS is to be output to the following path format. 
    * `/{container_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| region | - | enum | Enter the region of Object Storage product | [OBS Region in Detail](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#aws-sdk) |
| Bucket | - | string | Enter bucket name |  |
| Secret Key | - | string | Enter S3 API Credential Secret Key. |  |
| Access Key | - | string | Enter S3 API Credential Access Key. |  |
| Encoding | none | enum | Enter whether to encode or not . gzip encoding is available. |  |
| File Rotation Policy | size_and_time | enum | Determines file creation rules. | size_and_time – Use file size and time to decide<br>size – Use file size to decide <br>Time – Use time to decide |
| Standard time | 15 | number | Set the time to be the basis for file splitting.   | Set if file rotation policy is size_and_time or time |
| File size | 5242880 | number | Set the size to be the basis for file splitting.   | Set when file rotation policy is size_and_time or size |
| ACL | private | enum | Enter ACL policy to set when file is uploaded. |  |
| Storage Class | STANDARD | enum | Set Storage Class when file is uploaded. | [ Storage Class Guidel](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |

### Json Codec Output example exercise

#### condition

* Region => `KR1`
* Bucket => `obs-test-container`
* Access Key => `******`
* Secret Key => `******`

#### Input message

``` json
{"hidden":"Hello Dataflow!","message":"Hello World", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### Output Value

* Path

```
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* Output message

``` json
{"@timestamp":"2022-11-21T07:49:20.000Z","host":"755b65d82bd0","hidden":"Hello Dataflow!","@version":"1","sequence":0,"message":"Hello World"}
```

### plain Codec Output example exercise – when message field exists

#### condition

* Region => `KR1`
* Bucket => `obs-test-container`
* Access Key => `******`
* Secret Key => `******`

#### Input message

``` json
{ 
    "message": "Hello World!", 
    "hidden": "Hello Dataflow!", 
    "@timestamp": "2022-11-21T07:49:20Z" 
}
```

#### Output Value

* Path

```
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* Output message

```
2022-11-21T07:49:20.000Z e0e40e03dd94 Hello World
```

### plain Codec Output example exercise – when message field doesn’t exist

#### condition

* Region => `KR1`
* Bucket => `obs-test-container`
* Access Key => `******`
* Secret Key => `******`

#### Input message

``` json
{ 
    "hidden": "Hello Dataflow!", 
    "@timestamp": "2022-11-21T07:49:20Z" 
}
```

#### Output Value

* Path

```
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* Output message

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
```

## (Amazon) S3

### Node Description

* Node for uploading data to Amazon S3.

### Property Description 
| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| region | - | enum | Enter Region of S3 product. | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| Bucket | - | string | Enter bucket name |  |
| Access Key | - | string | Enter S3 API Credential Access Key. |  |
| Secret Key | - | string | Enter S3 API Credential Secret Key. |  |
| Signature Version | - | enum | Enter the version to use when signing AWS requests. |  |
| Session Token | - | string | Enter the Session Token for AWS temporary Credentials. | [ Session Token Guide](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) |
| Prefix | - | string | Enter Prefix before the name when uploading files. |  |
| Storage Class | STANDARD | enum | Set Storage Class when file is uploaded. | [Storage Class Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| Encoding | none | enum | Enter whether to encode or not . gzip encoding is available. |  |
| File Rotation Policy | size_and_time | enum | Determine file creation rules. | size_and_time – Use file size and time to decide<br>size – Use file size to decide <br>Time – Use time to decide |
| Standard time | 15 | number | Set the time to be the basis for file splitting.   | Set when the file rotation policy is size_and_time or time |
| File size | 5242880 | number | Set the size to be the basis for file splitting.   | Set when the file rotation policy is size_and_time or size |
| ACL | private | enum | Enter ACL policy to set when file is uploaded. |  |
| Additional Settings | { } | Hash | Enter additional settings to connect to S3. | [Guide](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html) |

### Output example exercise

* Same with OBS.

### Additional Settings example

#### follow redirects

* Track the redirect path,  when set to `true` and when AWS S3 returns 307 response 

``` js
{ 
    follow_redirects => true 
}
```

#### retry limit

* Maximum times of retries for 4xx, 5xx responses

``` js
{ 
    retry_limit => 5 
}
```

#### force_path_style

* For `true`, the URL must be path-style, not virtual-hosted-style. [Reference](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/VirtualHosting.html)

``` js
{ 
    force_path_style => true 
}
```

## Kafka

### Node Description

* Node for sending data to Kafka.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Topic | - | string | Type the name of Kafka topic to which want to send message. |  |
| Broker Server List | localhost:9092 | string | Enter Kafka Broker server. Separate multiple servers with commas (`, `). | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| Client ID | dataflow | string | Enter ID that identifies Kafka Producer. | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| Message Serialization Type | org.apache.kafka.common.serialization.StringSerializer | string | Enter how to serialize message value to send. | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| Compression type | none | enum | Enter how to compress data to send. | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br>Select out of none, gzip, snappy, lz4 |
| Key Serialization Type | org.apache.kafka.common.serialization.StringSerializer | string | Enter how to serialize message key to send. | [key.serializer](https://kafka.apache.org/documentation/#producerconfigs_key.serializer) |
| Meta data upload cycle | 300000 | number | Enter the interval (ms) at which want to update partition, broker server status, etc. | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms) |
| Maximum Request Size | 1048576 | number | Enter maximum size (byte) per transfer request. | [max.request.size](https://kafka.apache.org/documentation/#producerconfigs_max.request.size) |
| Server Reconnection Cycle | 50 | number | Enter how often to retry when Connection to broker server fails. | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms) |
| Batch Size | 16384 | number | Enter size (byte) to send to Batch Request. | [batch.size](https://kafka.apache.org/documentation/#producerconfigs_batch.size) |
| Buffer Memory | 33554432 | number | Enter size (byte) of buffer used to transfer Kafka. | [buffer.memory](https://kafka.apache.org/documentation/#producerconfigs_buffer.memory) |
| Receiving Buffer Size | 32768 | number | Enter size (byte) of TCP receive buffer used to read data. | [receive.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_receive.buffer.bytes) |
| Transfer Delay Time | 0 | number | Enter delay time for message sending. Delayed messages are sent as batch requests at once. | [linger.ms](https://kafka.apache.org/documentation/#producerconfigs_linger.ms) |
| Server Request Timeout | 40000 | number | Enter timeout (ms) for Transfer Request. | [request.timeout.ms](https://kafka.apache.org/documentation/#producerconfigs_request.timeout.ms) |
| Meta data Query Timeout |  | number |  | [https://kafka.apache.org/documentation/#upgrade_1100_notable](https://kafka.apache.org/documentation/#upgrade_1100_notable) |
| Transfer Buffer Size | 131072 | number | Enter size (byte) of TCP send buffer used to transfer data. | [send.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_send.buffer.bytes) |
| Ack Property  | 1 | enum | Enter settings to verify that messages have been received from Broker server. | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br>0 - Does not check if 6543eyu0=message is received.<br>1 - Leader of topic responds that the message was received without waiting for follower to copy data.<br>all - Leader of topic waits for follower to copy the data before responding that they have received the message. |
| Request Reconnection Cycle | 100 | number | Enter the interval (ms) to retry when transfer request fails. | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms) |
| Retry times | - | number | Enter the maximum times (ms) to retry when transfer request fails. | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br>Retrying exceeding the set value may result in data loss. |

### Json Codec Output example exercise

#### Input message

``` json
{ 
    "message": "Hello World!", 
    "hidden": "Hello Dataflow!", 
    "@timestamp": "2022-11-21T07:49:20Z" 
}
```

#### Output message

``` json
{"host":"0bc501d89f8c","message":"Hello World","hidden":"Hello Dataflow!","sequence":0,"@timestamp":"2022-11-21T07:49:20.000Z","@version":"1"}
```

### plain Codec Output example exercise

#### Input message

``` json
{ 
    "message": "Hello World!", 
    "hidden": "Hello Dataflow!", 
    "@timestamp": "2022-11-21T07:49:20Z" 
}
```

#### Output message

```
2022-11-21T07:49:20.000Z 2898d492114d Hello World
```

### plain Codec Output example exercise – when message field doesn’t exist

#### Input message

``` json
{ 
    "hidden": "Hello Dataflow!", 
    "@timestamp": "2022-11-21T07:49:20Z" 
}
```

#### Output message

```
2022-11-21T07:49:20.000Z e5ef7ece9bb0 %{message}
```

## Branch

* Node type that defines flow Quarter in accordance with imported data value.

## IF

### Node Description

* Node for filtering messages through conditional sentence.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| conditional sentence. | - | string | Enter the conditions for message filtering. |  |

### Filtering example exercise - first depth field reference

#### condition

* conditional sentence => `[logLevel] == "ERROR"`

#### Pass message

``` json
{ 
       "logLevel": "ERROR" 
}
```

#### Missed message

``` json
{ 
   "logLevel": "INFO" 
}
```

### Filtering example exercise - second depth field reference

#### condition

* conditional sentence => `[response][status] == 200`

#### Passed message

``` json
{ 
    "resposne": { 
        "status": 200 
    } 
}
```

#### Missed message

``` json
{ 
    "resposne": { 
        "status": 404 
    } 
}
```
