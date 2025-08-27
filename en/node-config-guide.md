## Data & Analytics > DataFlow > Node Type Guide

* Node types are pre-defined templates for easy flow creation.
* Types of node types are Source, Filter, Branch, and Sink.
* It is recommended that Source, Sink Node type have to be tested to ensure that Endpoint information is valid.
* You must use the DataFlow IP fixation feature when connecting to data sources with access control enabled.
    * To enable the DataFlow IP fixation feature, contact the Customer Center.

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
    * ex) {{ "2022-11-04T13:31:28Z" | startOf: MINUTE }}
        * → 2022-11-04T13:31:00Z
    * ex) {{ "2022-11-04T13:31:28Z" | startOf: HOUR }}
        * → 2022-11-04T13:00:00Z
    * ex) {{ "2022-11-04T13:31:28Z" | startOf: DAY }}
        * → 2022-11-04T00:00:00Z
    * ex) {{ "2022-11-04T13:31:28Z" | startOf: MONTH }}
        * → 2022-11-01T00:00:00Z
    * ex) {{ "2022-11-04T13:31:28Z" | startOf: YEAR }}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * Returns the last time of time zone defined by `unit` from the given time.
    * **Calculate based on Korean time.**
    * ex) {{ executionTime | endOf: MINUTE }}
    * ex) {{ "2022-11-04T13:31:28Z" | endOf: MINUTE }}
        * → 2022-11-04T13:31:59.999999999Z
    * ex) {{ "2022-11-04T13:31:28Z" | endOf: HOUR }}
        * → 2022-11-04T13:59:59.999999999Z
    * ex) {{ "2022-11-04T13:31:28Z" | endOf: DAY }}
        * → 2022-11-04T23:59:59.999999999Z
    * ex) {{ "2022-11-04T13:31:28Z" | endOf: MONTH }}
        * → 2022-11-30T23:59:59.999999999Z
    * ex) {{ "2022-11-04T13:31:28Z" | endOf: YEAR }}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * Returns the time subtracted by `delta` in the time zone defined by `unit` from the given time.
    * ex) {{ executionTime | subTime: 10, MINUTE }}
    * ex) {{ "2022-11-04T13:31:28Z" | subTime: 10, MINUTE }}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * Returns the time added by `delta` in the time zone defined by `unit` from the given time.
    * ex) {{ executionTime | addTime: 10, MINUTE }}
    * ex) {{ "2022-11-04T13:31:28Z" | addTime: 10, MINUTE }}
        * → 2022-11-04T13:41:28Z
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
    * ex) {{ "2022-11-04T13:31:28Z" | format: 'yyyy' }}
        * → 2022
* nested filter example
    * DSL expression at 03:00 hour on the day the flow started
        * → {{ executionTime | startOf: DAY | addTime: 3, HOUR }}

## Input by Data Type
### string
* Enter a string.

### number
* Enter a number greater or equal to 0.
* Use the arrow to the right of the input box to adjust the value by 1.

### boolean
* Select `TRUE` or `FALSE` from the drop-down menu.

### enum
* Select an item from the drop-down menu.

### array of strings
* Enter the strings that will go into the array one by one.
* After entering the string, click `+` to insert the string into the array.
* ex) If you want to enter `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`, insert the string into the array in the following order: `message`, `yyyy-MM-dd HH:mm:ssZ`, `ISO8601`.

### Hash
* Enter a string in JSON format.

## Source

* Node type that defines an endpoint that imports data to the flow.

### Execution Mode

* The Source node has two execution modes, BATCH and STREAMING.
    * STREAMING mode: Processes data in real time without exiting the flow.
    * BATCH mode: Processes a set amount of data and then terminates the flow.
* Different Source nodes support different execution modes.
* In a single flow, all source nodes must be set to the same execution mode.

## Common Settings on Source Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Type | - | string | Create `type` field with the value given in each message. |  |
| ID | - | string | Sets Node ID<br/>Mark the Node name on the chart board with values defined in this property. |  |
| Tag | - | array of strings | Add the tag of given value to each message. |  |
| Add Field | - | Hash | You can add a custom field<br/>You can add fields by calling in the value of each field with `%{[depth1_field]}`. |  |

## Example of adding fields

``` json
{ 
    "my_custom_field": "%{[json_body][logType]}" 
}
```

## (NHN Cloud) Log & Crash Search

### Node Description

* (NHN Cloud) Log & Crash Search Node is node that reads logs from Log & Crash Search.
* You can set the log query start time for a node. If not set, the log is read from the start of the flow.
* If no end time is entered in the node, logs are read in streaming format. If an end time is entered, logs are read up to the end time and the flow ends.
* ```Currently, session logs and crash logs are not supported.```
* Affected by tokens from Log & Crash Search's Log Search API.
    * If you don't have enough tokens, you need to contact Log & Crash Search.

### Execution Mode
* STREAMING: Continues processing data after the `Query Start time`.
* BATCH: Processes all data that falls between the `Query Start time` and the `Query End time` and ends the flow.

### Property Description 

| Property name       | Default value | Data type    | Description                                                                                                                                                     | Others |
|-----------|-----|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey    | -   | string | Enter the app key for Log & Crash Search.                                                                                                                         |    |
| SecretKey | -   | string | Enter the secret key for Log & Crash Search.                                                                                                                       |    |
| Query Start time  | -   | string | Enter the start time of log query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Query End time  | -   | string | Enter the end time of log query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Number of retries    | -   | number | Enter the maximum number of times to retry when a log query fails.                                                                                                                       |    |

* Set the number of retries
    * If the number of retries fails, no more log queries are attempted, and the flow ends.

### Message imported by codec

* Log&Crash Search covers data in format **JSON** by default.
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

### Execution Mode
* STREAMING: Continues processing data after the `Query Start time`.
* BATCH: Processes all data that falls between the `Query Start time` and the `Query End time` and ends the flow.

### Property Description 

| Property name      | Default value | Data type    | Description                                                                                                                                                      | Others |
|----------|-----|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey   | -   | string | Enter the app key for CloudTrail.                                                                                                                                  |    |
| Query Start time  | -   | string | Enter the start time of data query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Query End time  | -   | string | Enter the end time of data query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Number of retries    | -   | number | Enter the maximum number of times to retry when a data query fails.    

* Set the number of retries
    * If the number of retries fails, no more data queries are attempted, and the flow ends.

### Message imported by codec

* CloudTrail covers data in the format **JSON** by default.
* If no codec is selected or Plain, JSON string for CloudTrail data will be included as field called `message`.
* If you want to use each field in CloudTrail data, we recommend using json Codec.

#### Not selected or Plain

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

### Node Description

* Node that receives data from Object Storage of NHN Cloud.
* Based on the object creation time, data is read from the object created the earliest.

### Execution Mode
* STREAMING: Updates the object list on each `list update cycle`and processes data by reading newly added objects.
* BATCH: Fetches the object list once at the beginning of the flow, reads the objects, processes the data, and ends the flow.

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Bucket | - | string | Enter a bucket name to read data. |  |
| Region | - | string | Enter region information configured in the storage. |  |
| Secret Key | - | string | Enter the credential secret key issued by S3. |  |
| Access key | - | string | Enter the credential access key issued by S3. |  |
| List update cycle | - | number | Enter the object list update cycle included in the bucket. |  |
| Metadata included or not | - | boolean | Determine whether to include metadata from the S3 object as a key. In order to expose metadata fields to the Sink plugin, you need to combine filter node types (see guide below). | fields to be created are as follows.<br/>last_modified: The last time the object was modified<br/>content_length: Object size<br/>key: Object name<br/>content_type: Object type<br/>metadata: Metadata<br/>etag: etag |
| Prefix | - | string | Enter a prefix of an object to read. |  |
| Key pattern to exclude | - | string | Enter the pattern of an object not to be read. |  |
| Delete processed objects  | false | boolean | If the property value is true, delete the object read. |  |

### Metadata Field Usage

* When `Metadata included or not` is enabled, the metadata field is created, but is not exposed by the Sink plugin without injecting it as a regular field.
* Example message after (NHN Cloud) Object Storage plugin when activating settings
```js
}
    // General field
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "Object contents..."

    // Metadata fields
    // Cannot be exposed to the Sink plugin until the user injects it as a regular field
    // "[@metadata][s3][last_modified]": 2024-01-05T01:35:50.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][metadata]": {}
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
}
```

* Although the option to add fields exists in this (NHN Cloud) Object Storage Source plugin, it fails to add fields simultaneously with data import.
* Add it as a regular field via the Add field option in the common settings of any Filter plugin.
* Example of field additional options
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

* Example message after the alter (add field option) plugin
```js
}
    // General field
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "Object contents..."
    "last_modified": 2024-01-05T01:35:50.000Z
    "content_length": 220
    "key": "{filename}"
    "content_type": "text/plain"
    "metadata": {}
    "etag": "\"56ad65461e0abb907465bacf6e4f96cf\""

    // Metadata field
    // "[@metadata][s3][last_modified]": 2024-01-05T01:35:50.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][metadata]": {}
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
}
```

### Message imported by codec

#### Unselected or plain

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

### Node Description

* Node for uploading data to Amazon S3.
* Based on the object creation time, data is read from the object created the earliest.

### Execution Mode
* STREAMING: Updates the object list on each `list update cycle`and processes data by reading newly added objects.
* BATCH: Updates the object list once at the start of the flow, then reads the objects, processes the data, and ends the flow.

### Property Description 

| Property name | Default value | Data type | Description | Note                                                                                                                                                                                                           |
| --- | --- | --- | --- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Endpoint | - | string | Enter endpoint for S3 storage. | Only HTTP and HTTPS URL types can be entered.                                                                                                                                                                                |
| Bucket | - | string | Enter a bucket name to read data. |                                                                                                                                                                                                              |
| Region | - | string | Enter region information configured in the storage. |                                                                                                                                                                                                              |
| Session token | - | string | Enter AWS session token. |                                                                                                                                                                                                              |
| Secret Key | - | string | Enter the credential secret key issued by S3. |                                                                                                                                                                                                              |
| Access key | - | string | Enter the credential access key issued by S3. |                                                                                                                                                                                                              |
| List update cycle | - | number | Enter the object list update cycle included in the bucket. |                                                                                                                                                                                                              |
| Metadata included or not | - | boolean | Determine whether to include metadata from the S3 object as a key. In order to expose metadata fields to the Sink plugin, you need to combine filter node types (see guide below). | fields to be created are as follows.<br/>last_modified: The last time the object was modified<br/>content_length: Object size<br/>key: Object name<br/>content_type: Object type<br/>metadata: Metadata<br/>etag: etag |
| Prefix | - | string | Enter a prefix of an object to read. |                                                                                                                                                                                                              |
| Key pattern to exclude | - | string | Enter the pattern of an object not to be read. |                                                                                                                                                                                                              |
| Delete | false | boolean | If the property value is true, delete the object read. |                                                                                                                                                                                                              |
| Additional settings | - | hash | Enter additional settings to use when connecting to the S3 server. | See the following link for a full list of available settings.<br/>https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html<br/>Example:<br/>{<br/>"force_path_style": true<br/>}                                                     |

### Metadata Field Usage

* When `Metadata included or not` is enabled, the metadata field is created, but is not exposed by the Sink plugin without injecting it as a regular field.
* Example message after (Amazon) S3 plugin when activating settings
```js
{
    // General field
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "Object contents..."

    // Metadata fields
    // Cannot be exposed to the Sink plugin until the user injects it as a regular field
    // "[@metadata][s3][server_side_encryption]": "AES256"
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][last_modified]": 2024-01-05T02:27:26.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][metadata]": {}
}
```

* The option to add fields exists in this (Amazon) S3 Source plugin, but it fails to do so at the same time as the data is being imported.
* Add it as a regular field via the Add field option in the common settings of any Filter plugin.
* Example of field additional options
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

* Example message after the alter (add field option) plugin
```js
{
    // General field
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "파일 내용..."
    "server_side_encryption": "AES256"
    "etag": "\"56ad65461e0abb907465bacf6e4f96cf\""
    "content_type": "text/plain"
    "key": "{filename}"
    "last_modified": 2024-01-05T01:35:50.000Z
    "content_length": 220
    "metadata": {}

    // Metadata field
    // "[@metadata][s3][server_side_encryption]": "AES256"
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][last_modified]": 2024-01-05T02:27:26.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][metadata]": {}
}
```

### Message imported by codec

#### Unselected or plain

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

### Node Description

* Node that receives data from Kafka.

### Execution Mode
* STREAMING: Processes data every time a new message arrives in a topic.

!!! danger "Caution"
    * Kafka nodes do not support BATCH mode.

### Property Description 

| Property name | Default value | Data type | Description | Note                                                                                                                                                                                                                                                                                                                                                       |
| --- | --- | --- | --- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Broker server list | localhost:9092 | string | Enter the Kafka broker server. Separate multiple servers with commas ( , ). | [bootstrap.servers](https://kafka.apache.org/documentation/#consumerconfigs_bootstrap.servers)<br/>ex: 10.100.1.1:9092,10.100.1.2:9092                                                                                                                                                                                                                    |
| Consumer group ID | dataflow | string | Enter an ID that identifies the Kafka Consumer Group. | [group.id](https://kafka.apache.org/documentation/#consumerconfigs_group.id)                                                                                                                                                                                                                                                                             |
| Internal topic excluded or not | true | boolean |  | [exclude.internal.topics](https://kafka.apache.org/documentation/#consumerconfigs_exclude.internal.topics)<br/>Exclude internal topics such as `__consumer_offsets` from recipients.                                                                                                                                                                                            |
| Topic pattern | - | string | Enter a Kafka topic patter to receive messages. | ex: `*-messages`                                                                                                                                                                                                                                                                                                                                          |
| Client ID | dataflow | string | Enter an ID to identify Kafka Consumer. | [client.id](https://kafka.apache.org/documentation/#consumerconfigs_client.id)                                                                                                                                                                                                                                                                           |
| Partition allocation policy | - | string | Determines how Kafka assigns partitions to consumer groups when receiving messages. | [partition.assignment.strategy](https://kafka.apache.org/documentation/#consumerconfigs_partition.assignment.strategy)<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| Offset settings | none | enum | Enter  | [auto.offset.reset](https://kafka.apache.org/documentation/#consumerconfigs_auto.offset.reset)<br/>All of the settings below preserve the existing offset if the consumer group already exists.<br/>none: Return an error when there is no consumer group.<br/>earliest: Initialize to the partition’s oldest offset if there is no consumer group.<br/>latest: Initialize to the partition’s most recent offset if there is no consumer group.                                                                                                                                                                                                                                               |
| Offset commit cycle | 5000 | number | Enter a cycle to update the consumer group offset. | [auto.commit.internal.ms](https://kafka.apache.org/documentation/#consumerconfigs_auto.commit.interval.ms)                                                                                                                                                                                                                                               |
| Offset auto commit or not | true | boolean |  | [enable.auto.commit](https://kafka.apache.org/documentation/#consumerconfigs_enable.auto.commit)                                                                                                                                                                                                                                               |
| Key deserialization type | org.apache.kafka.common.serialization.StringDeserializer | string | Enter how to serialize the keys of incoming messages. | [key.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_key.deserializer)                                                                                                                                                                                                                                               |
| Message deserialization type | org.apache.kafka.common.serialization.StringDeserializer | string | Enter how to serialize the values of incoming messages. | [value.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_value.deserializer)                                                                                                                                                                                                                                               |
| Metadata created or not | false | boolean | If the property value is true, creates a metadata field for the message. You need to combine filter node types to expose metadata fields to the Sink plugin (see the guide below). | fields to be created are as follows.<br/>topic: Topic that receives message<br/>consumer_group: Consumer group ID used to receive messages<br/>partition: Topic partition number that receives messages<br/>offset: Partition offset that receives messages<br/>key: ByteBuffer that includes message keys                                                                                                                                                                                                                                               |
| Minimum Fetch size | - | number | Enter the minimum size of data to be imported in one fetch request. | [fetch.min.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.min.bytes)                                                                                                                                                                                                                                               |
| Transfer buffer size | - | number | Enter size (byte) of TCP send buffer used to transfer data.  | [send.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_send.buffer.bytes)                                                                                                                                                                                                                                               |
| Retry request cycle | 100 | number | Enter the retry cycle (ms) when a transfer request fails. | [retry.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_retry.backoff.ms)                                                                                                                                                                                                                                               |
| Cyclic redundancy check | true | enum | Check the message CRC. | [check.crcs](https://kafka.apache.org/documentation/#consumerconfigs_check.crcs)                                                                                                                                                                                                                                               |
| Server reconnection cycle | 50 | number | Enter a retry cycle when connecting to broker server fails. | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_reconnect.backoff.ms)                                                                                                                                                                                                                                               |
| Poll timeout | 100 | number | Enter the timeout (ms) for requests to fetch new messages from the topic. |                                                                                                                                                                                                                                               |
| Maximum fetch size per partition | - | number | Enter the maximum size to be imported in one fetch request per partition. | [max.partition.fetch.bytes](https://kafka.apache.org/documentation/#consumerconfigs_max.partition.fetch.bytes)                                                                                                                                                                                                                                               |
| Server request timeout | 30000 | number | Enter the timeout (ms) for sent request. | [request.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_request.timeout.ms)                                                                                                                                                                                                                                               |
| TCP receive buffer size | - | number | Enter the size in bytes of the TCP receive buffer used to read data. | [receive.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_receive.buffer.bytes)                                                                                                                                                                                                                                               |
| session_timeout_ms | - | number | Enter a session timeout (ms) of consumer.<br/>If a consumer fails to send a heartbeat within that time, it is excluded from the consumer group. | [session.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_session.timeout.ms)                                                                                                                                                                                                                                               |
| Maximum poll message count | - | number | Enter the maximum number of messages to retrieve in one poll request. | [max.poll.records](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.records)                                                                                                                                                                                                                                               |
| Maximum poll cycle | - | number | Enter the maximum cycle (ms) between poll requests. | [max.poll.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.interval.ms)                                                                                                                                                                                                                                               |
| Maximum Fetch size | - | number | Enter the maximum size to be imported in one fetch request. | [fetch.max.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.bytes)                                                                                                                                                                                                                                               |
| Maximum Fetch wait time | - | number | Enter the wait time (ms) to send a fetch request when data is not gathered as much as the minimum fetch size setting. | [fetch.max.wait.ms](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.wait.ms)                                                                                                                                                                                                                                               |
| Consumer health check cycle | - | number | Enter a cycle of consumer sending heartbeat. | [heartbeat.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_heartbeat.interval.ms)                                                                                                                                                                                                                                               |
| Metadata update cycle | - | number | Enter the cycle (ms) to update the partition, broker server status, etc. | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms)                                                                                                                                                                                                                                               |
| IDLE timeout | - | number | Enter the wait time (ms) to close a connection without data transmission. | [connections.max.idle.ms](https://kafka.apache.org/documentation/#consumerconfigs_connections.max.idle.ms)                                                                                                                                                                                                                                               |

### Metadata Field Usage

* When `Metadata created or not` is enabled, the metadata field is created, but is not exposed by the Sink plugin without injecting it as a regular field.
* Message examples after Kafka plugin when the setting is enabled
```js
{
    // normal fields
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafka topic message..."

    // metadata field
    // Cannot be exposed to the Sink plugin until user input data into normal fields
    // "[@metadata][kafka][topic]": "my-topic"
    // "[@metadata][kafka][consumer_group]": "my_consumer_group"
    // "[@metadata][kafka][partition]": "1"
    // "[@metadata][kafka][offset]": "123"
    // "[@metadata][kafka][key]": "my_key"
    // "[@metadata][kafka][timestamp]": "-1"
}
```

* There is an option to add fields in this Kafka Source plug-in, but adding fields cannot be performed at the same time as data is imported.
* Add as a general field through the additional field options among the common settings of any Filter plug-in.
* Example of field additional options
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
* Message examples after alter (additional field option) plugin
```js
{
    // normal field
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafka topic message..."
    "kafka_topic": "my-topic"
    "kafka_consumer_group": "my_consumer_group"
    "kafka_partition": "1"
    "kafka_offset": "123"
    "kafka_key": "my_key"
    "kafka_timestamp": "-1"

    // metadata field
    // "[@metadata][kafka][topic]": "my-topic"
    // "[@metadata][kafka][consumer_group]": "my_consumer_group"
    // "[@metadata][kafka][partition]": "1"
    // "[@metadata][kafka][offset]": "123"
    // "[@metadata][kafka][key]": "my_key"
    // "[@metadata][kafka][timestamp]": "-1"
}
```
        
### plain codec examples

#### Input message

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

#### Output message

```js
{
    "message": "{\"hello\":\"world\",\"hey\":\"foo\"}"
}
```

### json codec examples

#### Input messages

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

#### Output message

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

## Source > JDBC

### Node Description

* JDBC is a node that executes queries to the DB at a given interval to retrieve results.
* Supports MySQL, MS-SQL, PostgreSQL, MariaDB, and Oracle drivers.

### Execution Mode
* STREAMING: Processes data by executing a query at every `query execute frequency`.
* BATCH: Executes the query once at the beginning of the flow, processes the data, and ends the flow.

### Property Description

| Property name | Default value | Data type | Description | Note                                                                                          |
| --- | --- | --- | --- |---------------------------------------------------------------------------------------------|
| User | - | string | Enter a DB user. |                                                                                             |
| Connection String | - | string | Enter the DB connection information. | Example: `jdbc:mysql://my.sql.endpoint:3306/my_db_name`                                           |
| Password | - | string | Enter the user password. |                                                                                             |
| Query | - | string | Write a query to create a message. |                                                                                             |
| Whether to convert columns to lowercase | true | boolean | Determine whether to lowercase the column names you get as a result of the query. |                                                                                             |
| Query execute frequency | `* * * * *` | string | Enter the execute frequency of the query in a cron-like expression. |                                                                                             |
| Tracking Columns | - | string | Select the columns you want to track. | The predefined parameter `:SQL_LAST_VALUE`allows you to use a value corresponding to the column you want to track in the last query result.<br>See how to write a query below. |
| Tracking column type | array of strings | string | Select the type of data in the column you want to track. | Example: `numeric` or `timestamp`                                                                 |
| Time zone | - | string | Define the time zone to use when converting a column of type timestamp to a human-readable string. | Example: `Asia/Seoul`                                                                             |
| Whether to apply paging | true | boolean | Determines whether to apply paging to the query. | When paging is applied, the query is split into multiple executions, the order of which is not guaranteed.                                               |
| Page size | - | number | In a paged query, it determines how many pages to query at once. |                                                                                             |

### How to write a query

* `:sql_last_valu` allows you to use the value corresponding to `the tracking column`in the result of the last executed query (the default value is `0`, if the `tracking column type`is `numeric`, or `1970-01-01 00:00:00` , if it is `timestamp`).

``` sql
SELECT * FROM MY_TABLE WHERE id > :sql_last_value
```

* If you want to add a specific condition, add `:sql_last_value`in addition to the condition.

```sql
SELECT * FROM MY_TABLE WHERE id > :sql_last_value and id > custom_value order by id ASC
```

### Message imported by codec

#### Unselected or plain

``` js
{
    "id": 1,
    "name": "dataflow",
    "deleted": false
}
```

## Filter

* Node type that defines how to handle imported data.

## Common Settings on Filter Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| ID | - | string | Sets Node ID<br/>Mark node name on chart board with values defined in this property. |  |
| Add Tag | - | array of strings | Add Tag of each message |  |
| Delete Tag | - | array of strings | Delete Tag that was given to each message |  |
| Delete Field | - | array of strings | Delete Field of each message  |  |
| Add Field | - | Hash | You can add a custom field<br/>You can add fields by calling in the value of each Field with `%{[depth1_field]}`. |  |

## Filter > Alter

### Node Description

* Node that changes message field values to another values.
* You can only change the top-level fields.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Modify Field | - | array of strings | Compare the field value to a given value, if they are equal, modify the field value to the given value. |  |
| Overwrite Field | - | array of strings | Compare the field value to a given value, if they are equal, modify other field value to the given value. |  |
| Coalesce | - | array of strings | Assign a non-null value to the first of the fields that follow one field. |  |

### Order of applying settings

* Each setting is applied in the order listed in the property description.

#### Condition

* Modify Field → `["logType", "ERROR", "FAIL"]`
* Overwrite Field → `["logType", "FAIL", "isBillingTarget", "false"]`

#### Input message

```json
{
    "logType": "ERROR",
    "isBillingTarget": "true"
}
```

#### Output message

```json
{
    "logType": "FAIL",
    "isBillingTarget": "false"
}
```

### Example of Overwrite Field

#### Condition

* Overwrite Field → `["logType", "ERROR", "isBillingTarget", "false"]`

#### Input message

```json
{
    "logType": "ERROR",
    "isBillingTarget": "true"
}
```

#### Output message

```json
{
    "logType": "ERROR",
    "isBillingTarget": "false"
}
```

### Example of Modify Field

#### Condition

* Modify Field → `["reason", "CONNECTION_TIMEOUT", "MONGODB_CONNECTION_TIMEOUT"]`

#### Input message

```js
{
    "reason": "CONNECTION_TIMEOUT"
}
```

#### Output message

```js
{
    "reason": "MONGODB_CONNECTION_TIMEOUT"
}
```

### Coalesce Example

#### Condition

* Coalesce → `["reason", "%{webClientReason}", "%{mongoReason}", "%{redisReason}"]`

#### Input message

```js
{
    "mongoReason": "COLLECTION_NOT_FOUND"
}
```

#### Output message

```js
{
    "reason": "COLLECTION_NOT_FOUND",
    "mongoReason": "COLLECTION_NOT_FOUND"
}
```

## Filter > Cipher

### Node Description

* Node for decrypting message field values.
* For the encryption key, refer to symmetric keys in Secure Key Manager.
  * Secure Key Manager symmetric keys can be created through the Secure Key Manager web console or Add Key API in Secure Key Manager.
  * Even if a flow contains multiple Cipher nodes, all Cipher nodes can only refer to one Secure Key Manager's key reference.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Mode | - | enum | Choose between encryption mode and decryption mode. | Select one from the list. |
| Appkey | - | string | Enter SKM app key that saves the key for encryption/decryption. |  |
| Key ID | - | string | Enter SKM ID that saves the key for encryption/decryption. |  |
| Key Version | - | string | Enter SKM key version that saves the key for encryption/decryption. |  |
| Encryption/decryption key length | 16 | number | Enter encryption/decryption key length |  |
| IV Random Length | - | number | Enter random bytes length of Initial Vector.  |  |
| Source Field | - | string | Enter Field name for encryption/decryption. |  |
| Field to be stored | - | string | Enter Field name to save encryption/decryption result. |  |

### Encrypt example exercise 

#### condition

* mode → `encrypt`
* Appkey → `SKM appkey`
* Key ID → `SKM Symmetric key ID`
* Key Version → `1`
* IV Random Length → `16`
* Source Field → message
* Field to be  stored → encrypted_message

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

* mode → `decrypt`
* Appkey → `SKM appkey`
* Key ID → `SKM Symmetric key ID`
* Key Version → `1`
* IV Random Length → `16`
* Source Field → message
* Field to be  stored → decrypted_message

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

## Filter > (Logstash) Grok

### Node Description

* Node that parses a string according to a set rule and stores it in each set field.

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Match | - | hash | Enter the information of the string to be parsed. |  |
| Pattern definition | - | hash | Enter a custom pattern as a regular expression for the rule of tokens to be parsed. | Check the link below for system defined patterns.<br/>https://github.com/logstash-plugins/logstash-patterns-core/blob/main/patterns/legacy/grok-patterns |
| Failure tag | - | array of strings | Enter the tag name to define if string parsing fails. |  |
| Timeout | 30000 | number | Enter the amount of time to wait for string parsing. |  |
| Overwrite | - | array of strings | When writing a value to a designated field after parsing, if a value is already defined in the field, enter the field names to be overwritten. |  |
| Store only values with specified names | true | boolean | If the property value is true, do not store unnamed parting results. |  |
| Capture empty string | false | boolean | If the property value is true, store empty strings in fields. |  |
| Close or not when match | true | boolean | If the property value is true, the grok match result will terminate the plugin. |  |

### Grok parsing examples

#### Condition

* Match → `{ "message": "%{IP:clientip} %{HYPHEN} %{USER} [%{HTTPDATE:timestamp}] "%{WORD:verb} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}" %{NUMBER:response} %{NUMBER:bytes}" }`
* Pattern definition → `{ "HYPHEN": "-*" }`

#### Input messages

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326"
}
```

#### Output message

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

### Node Description

* Node that parses a message in CSV format and stores it in a field.

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Field to save | - | string | Enter the field name to save the CSV parsing result. |  |
| Quote | " | string | Enter the character that divides the column fields. |  |
| First line ignored or not | false | boolean | If the property value is true, the column name entered in the first row of the read data is ignored. |  |
| Column | - | array of strings | Enter the column name. |  |
| Separator | , | string | Enter a string to separate columns. |  |
| Source field | message | string | Enter the field name to parse CSV. |  |
| Schema | - | hash | Enter the name and data type of each column in the form of a dictionary. | Register separately from the fields defined in the column.<br/>The data type is basically a string, and if you need to convert to another data type, use the schema setting.<br/>Possible data types are as follows.<br/>integer, float, date, date_time, boolean |

### Examples of CSV parsing without data type

#### Condition

* Source field → `message`
* Column → `["one", "two", "t hree"]`

#### Input messages

```js
{
    "message": "hey,foo,\\\"bar baz\\\""
}
```

#### Output message

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```

### Examples of CSV parsing without data type

#### Condition

* Source field → `message`
* Column → `["one", "two", "t hree"]`

#### Input messages

```js
{
    "message": "hey,foo,\\\"bar baz\\\""
}
```

#### Output message

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```

### Examples of CSV parsing that requires data type conversion

#### Condition

* Source field → `message`
* Column → `["one", "two", "t hree"]`
* Schema →`{"two": "integer", "t hree": "boolean"}`

#### Input messages

```js
{
    "message": "\\\"wow hello world!\\\", 2, false"
}
```

#### Output message

```js
{
    "message": "\\\"wow hello world!\\\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### Node Description

* Node that parses a JSON string and stores it in a specified field.

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Source field | message | string | Enter a field name to parse JSON strings. |  |
| Field to save | - | string | Enter the field name to save the JSON parsing result.<br/>If no property value is specified, the result is stored in the root field. |  |

### JSON 

#### Condition

* Source field → `message`
* Field to store → `json_parsed_messsage`

#### Input messages

```js
{
    "message": "{\\\"json\\\": \\\"parse\\\", \\\"example\\\": \\\"string\\\"}"
}
```

#### Output message

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

### Node Description

* This is a node that parses a string according to defined rules and stores it in each set field.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Match | - | json | Enter the information of the string to be parsed. |  |
| Pattern definition | - | json | Enter a custom pattern as a regular expression for the rule of tokens to be parsed. | Check the link below for system defined patterns.<br/>http://grokdebug.herokuapp.com/patterns |
| Failure tag | - | array of strings | Enter the tag name to define if string parsing fails. |  |
| Timeout | 30000 | number | Enter the amount of time to wait for string parsing. |  |
| Overwrite | - | array of strings | When writing a value to a designated field after parsing, if a value is already defined in the field, enter the field names to be overwritten. |  |
| Store only values with specified names | - | boolean | Select whether to store unnamed parsing results. |  |
| Capture empty string | - | boolean | Select whether to store empty strings in fields. |  |

### Grok Parsing Examples

#### Condition

* Match → `{ "message": "%{IP:clientip} %{HYPHEN} %{USER} [%{HTTPDATE:timestamp}] "%{WORD:verb} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}" %{NUMBER:response} %{NUMBER:bytes}" }`
* Pattern definition → `{ "HYPHEN": "-*" }`

#### Input message

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326"
}
```

#### Output message

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

### Node Description

* A node that parses a data string and stores it in timestamp format.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Source Field | - | string | Enter a field name to get strings. |  |
| Formats | - | array of strings | Enter formats to get strings. | The pre-defined formats are as follows.<br/>ISO8601, UNIX, UNIX_MS, TAI64N |
| Locale | - | Enter a locale to use for string analysis. | ex) en, en-US, ko-kr |
| Field to be stored | - | string | Enter a field name to store the result of parsing data strings. |  |
| Failure tag | - | array of strings | Enter the tag name to define if data string parsing fails. |  |
| Time zone | - | string | Enter the time zone for the date. |  |

### Examples of Date String Parsing

#### Condition

* Match → `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* Field to be stored → `time`
* Time zone → `Asia/Seoul`

#### Input message

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### Output message

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### Node Description

* A node that creates UUIDs and stores them in a field.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Field to store UUID | - | string | Enter a field name to store UUID creation result. |  |
| Overwrite | - | boolean | Select whether to overwrite the value if it exists in the specified field name. |  |

### Example of UUID Creation

#### Condition

* Field to store UUID → `userId`

#### Input message

```js
{
    "message": "uuid test message"
}
```

#### Output message

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Split

### Node Description

* A node that splits a single message into multiple messages.
* Splits the message based on the result of parsing according to the settings.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Source field | - | string | Enter a field name to separate messages. |  |
| Field to be stored | - | string | Enter a field name to store separated messages. |  |
| Separator | `\n` | string |  |  |

### Example of Default Message Split

#### Condition

* Source field → `message`

#### Input message

```js
{
    "message": [
        {"number": 1},
        {"number": 2}
    ]
}
```

#### Output message

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

### Example of Split after String Parsing

#### Condition

* Source field → `message`
* Separator → `,`

#### Input message

```js
{
    "message": "1,2"
}
```

#### Output message

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

### Example of String Parsing and Splitting Into Different Fields

#### Condition

* Source field → `message`
* Field to be stored → `target`
* Separator → `,`

#### Input message

```js
{
    "message": "1,2"
}
```

#### Output message

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

### Node Description

* A node that parses JSON strings and stores it in a specified field.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Byte length | - | number | Enter the maximum byte length to represent a string. |  |
| Source field | - | string | Enter a field name for truncate. |  |

### JSON Parsing Example

#### Condition

* Byte length → 10
* Source field → `message`

#### Input message

```js
{
    "message": "This message is too long."
}
```

#### Output message

```js
{
    "message": "This message"
    }
```

## Filter > Mutate

### Node Description

* A node that transforms the value of a field.

### Property Description

| Property name | Default value | Data type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Set Defaults | - | Hash | Replace null with defaults. |  |
| Rename Field | - | Hash | Rename the field. |  |
| Update field values | - | Hash | Replaces the field value with the new value. If the field does not exist, no action is taken. |  |
| Replace Value | - | Hash | Replace the field value with a new value. If there is no field, create a new field.  |  |
| Convert Type | - | Hash | Convert the field value to another type. | The following types are supported: integer, interger_eu, float, float_eu, string, and boolean. |
| Replace String | - | array | Replace the part of string with regular expression. |  |
| Uppercase Letter | - | array | Uppercase Letter the string in the target field to uppercase letter. |  |
| Capitalize First Letter | - | array | Convert the first letter in the target field to uppercase letter, and the rest to lowercase letter. |  |
| Lowercase Letter | - | array | Lowercase the string in the target field to lowercase letter. |  |
| Strip Space | - | array | Remove spaces before and after the string in the target field. |  |
| Split String | - | Hash | Split strings using separators. |  |
| Join Array | - | Hash | Join array elements to separator. |  |
| Merge Field | - | Hash | Merge the two fields. |  |
| Copy Field | - | Hash | Copy the existing field to another field. If the field exists, overwrite field. |  |
| Failure Tag | \_mutate_error | string | Enter a tag to define if an error occurs. |  |

### Order of applying settings
* Each setting is applied in the order listed in the property description.

#### Condition

* Update Field Value → `{"fieldname": "new value"}`
* Uppercase Letter → `["fieldname"]`

#### Input message

```json
{
    "fieldname": "old value"
}
```

#### Output message

```json
{
    "fieldname": "NEW VALUE"
}
```

### Example of setting default

#### Condition

```json
{
  "fieldname": "default_value"
}
```

#### Input message

```json
{
  "fieldname": null
}
```

#### Output message

```json
{
  "fieldname": "default_value"
}
```

### Example of renaming a field

#### Condition
```json
{
  "fieldname": "changed_fieldname"
}
```

#### Input message

```json
{
  "fieldname": "Hello World!"
}
```

#### Output message

```json
{
  "changed_fieldname": "Hello World!"
}
```

### Example of updating field values

#### Condition

```json
{
  "fieldname": "%{other_fieldname}: %{fieldname}",
  "not_exist_fieldname": "DataFlow"
}
```

#### Input message

```json
{
  "fieldname": "Hello World!",
  "other_fieldname": "DataFlow"
}
```

#### Output message

```json
{
  "fieldname": "DataFlow: Hello World!",
  "other_fieldname": "DataFlow"
}
```

### Example of replacing values

#### Condition

```json
{
  "fieldname": "%{other_fieldname}: %{fieldname}",
  "not_exist_fieldname": "DataFlow"
}
```

#### Input message

```json
{
  "fieldname": "Hello World!",
  "other_fieldname": "DataFlow"
}
```

#### Output message

```json
{
  "fieldname": "DataFlow: Hello World!",
  "other_fieldname": "DataFlow",
  "not_exist_fieldname": "DataFlow"
}
```

### Example of converting types

#### Condition

```
{
  "message1": "integer",
  "message2": "boolean"
}
```

#### Input message

```json
{
  "message1": "1000",
  "message2": "true"
}
```

#### Output message

```json
{
    "message1": 1000,
    "message2": true
}
```

#### Type Description

* integer
  * Converts strings to integers. Supports comma-separated strings. Fractional parts are discarded.
    * Example: "1,000.5" -> `1000`
  * Converts floats to integers. Fractional parts are discarded.
  * Converts boolean values to integers: `true` is converted to `1`, `false` to `0`.
* integer_eu
  * Converts data to integers. Supports dot-separated strings. Fractional parts are discarded. 
    * Example: "1.000,5" -> `1000`
  * Float and boolean values are treated the same as integers.
* float
  * Converts integers to floats.
  * Converts strings to floats. Supports comma-separated strings.
    * Example: "1,000.5" -> `1000.5`
  * Converts boolean values to integers: `true`is converted to `1.0`, `false` to `0.0`.
* float_eu
  * Converts data to floats. Supports dot-delimited strings.
    * Example: "1.000,5" -> `1000.5`
  * Float and boolean values are treated the same as floats.
* string
  * Converts data to strings with UTF-8 encoding.
* boolean
  * Converts integers to boolean values. A `1`is converted to `true` and a `0` to `false`.
  * Converts floats to boolean values. A `1.0`is converted to `true`, and a `0.0` to `false`.
  * Converts strings to boolean values: `"true"`, `"t"`, `"yes"`, `"y"`, `"1"`, `"1.0"`are converted to `true`, `"false"`, `"f"`, `"no"`, `"n"`, `“0"`, `“0.0"`are converted to `false`. The empty strings are converted to`false`.
* Array data elements are converted as described above.

### Example of replacing string

#### Condition

```json
["fieldname", "/", "_", "fieldname2", "[\\?#-]", "."]
```
* Replace `/` with `_` in the string values in the `fieldname` field and `\`, `?`, `#`, and `-`with `.` in the string values in the `fieldname2` field.

#### Input message
```json
{
  "fieldname": "Hello/World",
  "fieldname2": "Hello\\?World#Test-123"
}
```

#### Output message
```json
{
  "fieldname": "Hello_World",
  "fieldname2": "Hello.World.Test.123"
}
```

### Example of uppercase letter

#### Condition

```json
["fieldname"]
```

#### Input message

```json
{
  "fieldname": "hello world"
}
```

#### Output message

```json
{
  "fieldname": "HELLO WORLD"
}
```

### Example of capitalizing the first letter

#### Condition

```json
["fieldname"]
```

#### Input message

```json
{
  "fieldname": "hello world"
}
```

#### Output message

```json
{
  "fieldname": "Hello world"
}
```

### Example of lowercase letter
#### Condition

```json
["fieldname"]
```

#### Input message

```json
{
  "fieldname": "HELLO WORLD"
}
```

#### Output message

```json
{
  "fieldname": "hello world"
}
```

### Example of stripping spaces

#### Condition

```json
["field1", "field2"]
```

#### Input message

```json
{
  "field1": "Hello World!   ",
  "field2": "   Hello DataFlow!"
}
```

#### Output message

```json
{
  "field1": "Hello World!",
  "field2": "Hello DataFlow!"
}
```

### Example of splitting strings

#### Condition

```json
{
  "fieldname": ","
}
```

#### Input message

```json
{
  "fieldname": "Hello,World"
}
```

#### Output message

```json
{
  "fieldname": ["Hello", "World"]
}
```

### Example of joining arrays

#### Condition

```json
{
  "fieldname": ","
}
```

#### Input Data

```json
{
  "fieldname": ["Hello", "World"]
}
```

#### Output message

```json
{
  "fieldname": "Hello,World"
}
```

### Example of merging fields
#### Condition

```json
{
  "array_data1": "string_data1",
  "string_data2": "string_data1",
  "json_data1": "json_data2"
}
```

#### Input message

```json
{
  "array_data1": ["array_data1"],
  "string_data1": "string_data1",
  "string_data2": "string_data2",
  "json_data1": {"json_field1": "json_data1"},
  "json_data2": {"json_field2": "json_data2"}
}
```

#### Output message

```json
{
  "array_data1": ["array_data1", "string_data1"],
  "string_data1": "string_data1",
  "string_data2": ["string_data2", "string_data1"],
  "json_data1": {"json_field2" : "json_data2", "json_field1": "json_data1"},
  "json_data2": {"json_field2": "json_data2"}
}
```
* array + array = merge two arrays
* array + string = add string to array
* string + string = convert two strings to an array with two strings as elements
* json + json = json merge

### Example of copying fields

#### Condition

```json
{
  "source_field": "dest_field"
}
```

#### Input message

```json
{
  "source_field": "Hello World!"
}
```

#### Output message

```json
{
  "source_field": "Hello World!",
  "dest_field": "Hello World!"
}
```

## Sink

* Type of node that defines an endpoint to load data that has completed filter operation.

## Common Settings on Sink Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| ID | - | string | Sets Node ID<br/>Mark node name on the chart board with values defined in this property. |  |

## (NHN Cloud) Object Storage

### Node Description

* Node for uploading data to Object Storage in NHN Cloud.
* Object created on OBS is output in the following path format by default. 
    * `/{container_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| region | - | enum | Enter the region of Object Storage product |  |
| Bucket | - | string | Enter bucket name |  |
| Secret Key | - | string | Enter S3 API Credential Secret Key. |  |
| Access Key | - | string | Enter S3 API Credential Access Key. |  |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | Enter a prefix to prefix the name when uploading the object.<br/>You can enter a field or time format. | [Available Time Format](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | @timestamp | string | Enter a time field to apply to the prefix. |  |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter a time field type to apply to the prefix. |  |
| Prefix Time Zone | UTC | string | Enter a time zone for the Time field to apply to the prefix. |  |
| Prefix Time Application fallback  | _prefix_datetime_parse_failure | string | Enter a prefix to replace if the prefix time application fails. |  |
| Encoding | none | enum | Enter whether to encode or not . gzip encoding is available. |  |
| Object Rotation Policy | size_and_time | enum | Determines object creation rules. | size_and_time – Use object size and time to decide<br/>size – Use object size to decide <br/>Time – Use time to decide |
| Reference Time | 15 | number | Set the time to be the basis for object splitting.   | Set if object rotation policy is size_and_time or time |
| Object size | 5242880 | number | Set the size to be the basis for object splitting.   | Set when object rotation policy is size_and_time or size |

### Json Codec Output example exercise

#### condition

* Region → `KR1`
* Bucket → `obs-test-container`
* Access Key → `******`
* Secret Key → `******`

#### Input message

``` json
{"hidden":"Hello Dataflow!","message":"Hello World", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### Output Value

* Path

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* Output message

``` json
{"@timestamp":"2022-11-21T07:49:20.000Z","host":"755b65d82bd0","hidden":"Hello Dataflow!","@version":"1","sequence":0,"message":"Hello World"}
```

### plain Codec Output example exercise – when message field exists

#### condition

* Region → `KR1`
* Bucket → `obs-test-container`
* Access Key → `******`
* Secret Key → `******`

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
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* Output message

```
2022-11-21T07:49:20.000Z e0e40e03dd94 Hello World
```

### plain Codec Output example exercise – when message field doesn’t exist

#### condition

* Region → `KR1`
* Bucket → `obs-test-container`
* Access Key → `******`
* Secret Key → `******`

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
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* Output message

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
```

### Parquet Codec Property Description

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| parquet compression codec | SNAPPY | enum | Enter the compression codec to use when converting PARQUET files. | [Reference](https://parquet.apache.org/docs/file-format/data-pages/compression/) |

### Prefix Example - Field

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/%{deployment}`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path

```
/obs-test-container/dataflow/production/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

### Prefix Example - Hour

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix Time Field → `logTime`
* Prefix Time Field Type → `ISO8601`
* Prefix Time Zone → `Asia/Seoul`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path

```
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

### Prefix Example - When failed to apply time

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix Time Field → `logTime`
* Prefix Time Field Type → `TIMESTAMP_SEC`
* Prefix Time Zone → `Asia/Seoul`
* Prefix Time Application fallback → `_failure`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path

```
/obs-test-container/_failure/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

## Sink > (Amazon) S3

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
| Session Token | - | string | Enter the Session Token for AWS temporary Credentials. | [ Session Token Guide](https://docs.aws.amazon.com/en_kr/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) |
| Prefix | - | string | Enter a prefix to prefix the name when uploading the object.<br/>You can enter a field or time format. | [Available Time Format](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | @timestamp | string | Enter a time field to apply to the prefix. |  |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter a time field type to apply to the prefix. |  |
| Prefix Time Zone | UTC | string | Enter a time zone for the Time field to apply to the prefix. |  |
| Prefix Time Application fallback  | _prefix_datetime_parse_failure | string | Enter a prefix to replace if the prefix time application fails. |  |
| Storage Class | STANDARD | enum | Set Storage Class when object is uploaded. | [Storage Class Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| Encoding | none | enum | Enter whether to encode or not . gzip encoding is available. |  |
| Object Rotation Policy | size_and_time | enum | Determine object creation rules. | size_and_time – Use object size and time to decide<br/>size – Use object size to decide <br/>Time – Use time to decide |
| Reference Time | 15 | number | Set the time to be the basis for object splitting.   | Set when the object rotation policy is size_and_time or time |
| Object size | 5242880 | number | Set the size to be the basis for object splitting.   | Set when the object rotation policy is size_and_time or size |
| ACL | private | enum | Enter ACL policy to set when object is uploaded. |  |
| Additional Settings | { } | Hash | Enter additional settings to connect to S3. | [Guide](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html) |

### Output example exercise

* Same with OBS.

### Parquet Codec Property Description

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| parquet compression codec | SNAPPY | enum | Enter the compression codec to use when converting PARQUET files. | [Reference](https://parquet.apache.org/docs/file-format/data-pages/compression/) |

### Additional Settings example

#### follow redirects

* Track the redirect path,  when set to `true` and when AWS S3 returns 307 response 

``` js
{ 
    follow_redirects → true 
}
```

#### retry limit

* Maximum times of retries for 4xx, 5xx responses

``` js
{ 
    retry_limit → 5 
}
```

#### force\_path\_style

* For `true`, the URL must be path-style, not virtual-hosted-style. [Reference](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/VirtualHosting.html)

``` js
{ 
    force_path_style → true 
}
```

## Sink > (Apache) Kafka

### Node Description

* Node for sending data to Kafka.

### Property Description 

| Property name           | Default value                                                    | Data type    | Description                                                 | Others                                                                                                                                                                                                                                               |
|---------------|--------------------------------------------------------|--------|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Topic            | -                                                      | string | Type the name of Kafka topic to which want to send message.                       |                                                                                                                                                                                                                                                  |
| Broker Server List     | localhost:9092                                         | string | Enter Kafka Broker server. Separate multiple servers with commas (`, `).  | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>ex: 10.100.1.1:9092,10.100.1.2:9092                                                                                                            |
| Client ID     | dataflow                                                | string | Enter ID that identifies Kafka Producer.                     | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id)                                                                                                                                                                    |
| Message Serialization Type    | org.apache.kafka.common.serialization.StringSerializer | string | Enter how to serialize message value to send.                       | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer)                                                                                                                                                     |
| Compression type         | none                                                   | enum   | Enter how to compress data to send.                           | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br/>Select out of none, gzip, snappy, lz4                                                                                                                       |
| Key Serialization Type      | org.apache.kafka.common.serialization.StringSerializer | string | Enter how to serialize message key to send.                       | [key.serializer](https://kafka.apache.org/documentation/#producerconfigs_key.serializer)                                                                                                                                                         |
| Meta data upload cycle   | 300000                                                 | number | Enter the interval (ms) at which want to update partition, broker server status, etc.               | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms)                                                                                                                                                         |
| Maximum Request Size      | 1048576                                                | number | Enter maximum size (byte) per transfer request.                         | [max.request.size](https://kafka.apache.org/documentation/#producerconfigs_max.request.size)                                                                                                                                                     |
| Server Reconnection Cycle     | 50                                                     | number | Enter how often to retry when Connection to broker server fails.                 | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms)                                                                                                                                             |
| Batch Size         | 16384                                                  | number | Enter size (byte) to send to Batch Request.                       | [batch.size](https://kafka.apache.org/documentation/#producerconfigs_batch.size)                                                                                                                                                                 |
| Buffer Memory        | 33554432                                               | number | Enter size (byte) of buffer used to transfer Kafka.                | [buffer.memory](https://kafka.apache.org/documentation/#producerconfigs_buffer.memory)                                                                                                                                                           |
| Receiving Buffer Size      | 32768                                                  | number | Enter size (byte) of TCP receive buffer used to read data.    | [receive.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_receive.buffer.bytes)                                                                                                                                             |
| Transfer Delay Time      | 0                                                      | number | Enter delay time for message sending. Delayed messages are sent as batch requests at once. | [linger.ms](https://kafka.apache.org/documentation/#producerconfigs_linger.ms)                                                                                                                                             |
| Server Request Timeout    | 40000                                                  | number | Enter timeout (ms) for Transfer Request.                         | [request.timeout.ms](https://kafka.apache.org/documentation/#producerconfigs_request.timeout.ms)                                                                                                                                                 |
| Meta data Query Timeout                                                        |  | number |                                                    | [https://kafka.apache.org/documentation/#upgrade_1100_notable](https://kafka.apache.org/documentation/#upgrade_1100_notable)                                                                                                                   |
| Transfer Buffer Size      | 131072                                                 | number | Enter size (byte) of TCP send buffer used to transfer data.     | [send.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_send.buffer.bytes)                                                                                                                                                   |
| Ack Property        | 1                                                      | enum   | Enter settings to verify that messages have been received from Broker server.                 | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br/>0 - Does not check if 6543eyu0=message is received.<br/>1 - Leader of topic responds that the message was received without waiting for follower to copy data.<br/>all - Leader of topic waits for follower to copy the data before responding that they have received the message. |
| Request Reconnection Cycle     | 100                                                    | number | Enter the interval (ms) to retry when transfer request fails.                  | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms)                                                                                                                                                     |
| Retry times        | -                                                      | number | Enter the maximum times (ms) to retry when transfer request fails.                   | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br/>Retrying exceeding the set value may result in data loss.                                                                                                                            |

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

## Sink > Stdout

### Node Description

* Node that outputs messages to standard output.
* This is useful for checking the data processed by the Source, Filter nodes.

### Example output by codec

#### Input message

``` json
{
    "host": "data-flow-01",
    "message": "Hello World!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### Plain codec output message

```
2022-11-21T07:49:20Z data-flow-01 Hello World!
```

#### JSON codec output example

```
{"host": "data-flow-01", "message": "Hello World!", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### LINE codec output example

* format is set to
  * `%{message} %{host}`

```
Hello World! data-flow-01
```

#### Debug codec output example

```
{
        "host" => "data-flow-01",
     "message" => "Hello World!",
  "@timestamp" => 2022-11-21T07:49:20Z
}
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

* conditional sentence → `[logLevel] == "ERROR"`

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

* conditional sentence → `[response][status] == 200`

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
