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

## Source

* Node type that defines an endpoint that imports data to the flow.

## Common Settings on Source Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Type | - | string | Create `type` field with the value given in each message. |  |
| Activate Measurement Items  | true | boolean | Collect metrics for nodes.<br/>If Property value is true, you can check the event metric information for node in the Monitoring tab. |  |
| ID | - | string | Sets Node ID<br/>Mark the Node name on the chart board with values defined in this property. |  |
| Tag | - | array of string | Add the tag of given value to each message. |  |
| Add Field | - | Hash | You can add a custom field<br/>You can add fields by calling in the value of each field with `%{[depth1_field]}`. |  |

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
| SecretKey | - | string | Enter the secret key for Log & Crash Search. |  |
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

## Source > (NHN Cloud) Object Storage

### Node Description

* Node that receives data from Object Storage of NHN Cloud.
* Based on the object creation time, data is read from the object created the earliest.

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Bucket | - | string | Enter a bucket name to read data. |  |
| Region | - | string | Enter region information configured in the storage. |  |
| Secret Key | - | string | Enter the credential secret key issued by S3. |  |
| Access key | - | string | Enter the credential access key issued by S3. |  |
| List update cycle | - | number | Enter the object list update cycle included in the bucket. |  |
| New file checked or not | - | boolean | If the property value is true, the newly added object is also read. |  |
| Prefix | - | string | Enter a prefix of an object to read. |  |
| Key pattern to exclude | - | string | Enter the pattern of an object not to be read. |  |
| Delete | false | boolean | If the property value is true, delete the object read. |  |

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

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Endpoint | - | string | Enter endpoint for S3 storage. | Only HTTP and HTTPS URL types can be entered. |
| Bucket | - | string | Enter a bucket name to read data. |  |
| Region | - | string | Enter region information configured in the storage. |  |
| Session token | - | string | Enter AWS session token. |  |
| Secret Key | - | string | Enter the credential secret key issued by S3. |  |
| Access key | - | string | Enter the credential access key issued by S3. |  |
| List update cycle | - | number | Enter the object list update cycle included in the bucket. |  |
| Meta information included or not | - | boolean | Determines whether to include the metadata of S3 objects as keys. | (Last modified time, Content-Type, etc.) |
| Prefix | - | string | Enter a prefix of an object to read. |  |
| Key pattern to exclude | - | string | Enter the pattern of an object not to be read. |  |
| Additional settings | - | hash | Enter additional settings to use when connecting to the S3 server. | See the following link for a full list of available settings.<br/>https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html<br/>Example)<br/>{<br/>"force_path_style": true<br/>} |

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

### Property Description 

| Property name | Default value | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Broker server list | localhost:9092 | string | Enter the Kafka broker server. Separate multiple servers with commas ( , ). | [bootstrap.servers](https://kafka.apache.org/documentation/#consumerconfigs_bootstrap.servers)<br/>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| Consumer group ID | dataflow | string | Enter an ID that identifies the Kafka Consumer Group. | [group.id](https://kafka.apache.org/documentation/#consumerconfigs_group.id) |
| Internal topic excluded or not | true | boolean |  | [exclude.internal.topics](https://kafka.apache.org/documentation/#consumerconfigs_exclude.internal.topics)<br/>Exclude internal topics such as `__consumer_offsets` from recipients. |
| Topic pattern | - | string | Enter a Kafka topic patter to receive messages. | ex) `*-messages` |
| Client ID | dataflow | string | Enter an ID to identify Kafka Consumer. | [client.id](https://kafka.apache.org/documentation/#consumerconfigs_client.id) |
| Partition allocation policy | - | string | Determines how Kafka assigns partitions to consumer groups when receiving messages. | [partition.assignment.strategy](https://kafka.apache.org/documentation/#consumerconfigs_partition.assignment.strategy)<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| Offset settings | none | enum | Enter  | [auto.offset.reset](https://kafka.apache.org/documentation/#consumerconfigs_auto.offset.reset)<br/>All of the settings below preserve the existing offset if the consumer group already exists.<br/>none: Return an error when there is no consumer group.<br/>earliest: Initialize to the partition’s oldest offset if there is no consumer group.<br/>latest: Initialize to the partition’s most recent offset if there is no consumer group. |
| Offset commit cycle | 5000 | number | Enter a cycle to update the consumer group offset. | [auto.commit.internal.ms](https://kafka.apache.org/documentation/#consumerconfigs_auto.commit.interval.ms) |
| Offset auto commit or not | true | boolean |  | [enable.auto.commit](https://kafka.apache.org/documentation/#consumerconfigs_enable.auto.commit) |
| Key deserialization type | org.apache.kafka.common.serialization.StringDeserializer | string | Enter how to serialize the keys of incoming messages. | [key.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_key.deserializer) |
| Message deserialization type | org.apache.kafka.common.serialization.StringDeserializer | string | Enter how to serialize the values of incoming messages. | [value.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_value.deserializer) |
| Metadata created or not | false | boolean | If the property value is true, creates a metadata field for the message. You need to combine filter node types to utilize metadata fields (see the guide below). | fields to be created are as follows.<br/>topic: Topic that receives message<br/>consumer_group: Consumer group ID used to receive messages<br/>partition: Topic partition number that receives messages<br/>offset: Partition offset that receives messages<br/>key: ByteBuffer that includes message keys |
| Minimum Fetch size | - | number | Enter the minimum size of data to be imported in one fetch request. | [fetch.min.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.min.bytes) |
| Transfer buffer size | - | number | Enter size (byte) of TCP send buffer used to transfer data.  | [send.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_send.buffer.bytes) |
| Retry request cycle | 100 | number | Enter the retry cycle (ms) when a transfer request fails. | [retry.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_retry.backoff.ms) |
| Cyclic redundancy check | true | enum | Check the message CRC. | [check.crcs](https://kafka.apache.org/documentation/#consumerconfigs_check.crcs) |
| Server reconnection cycle | 50 | number | Enter a retry cycle when connecting to broker server fails. | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_reconnect.backoff.ms) |
| Poll timeout | 100 | number | Enter the timeout (ms) for requests to fetch new messages from the topic. |  |
| Maximum fetch size per partition | - | number | Enter the maximum size to be imported in one fetch request per partition. | [max.partition.fetch.bytes](https://kafka.apache.org/documentation/#consumerconfigs_max.partition.fetch.bytes) |
| Server request timeout | 30000 | number | Enter the timeout (ms) for sent request. | [request.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_request.timeout.ms) |
| TCP receive buffer size | - | number | Enter the size in bytes of the TCP receive buffer used to read data. | [receive.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_receive.buffer.bytes) |
| session_timeout_ms | - | number | Enter a session timeout (ms) of consumer.<br/>If a consumer fails to send a heartbeat within that time, it is excluded from the consumer group. | [session.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_session.timeout.ms) |
| Maximum poll message count | - | number | Enter the maximum number of messages to retrieve in one poll request. | [max.poll.records](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.records) |
| Maximum poll cycle | - | number | Enter the maximum cycle (ms) between poll requests. | [max.poll.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.interval.ms) |
| Maximum Fetch size | - | number | Enter the maximum size to be imported in one fetch request. | [fetch.max.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.bytes) |
| Maximum Fetch wait time | - | number | Enter the wait time (ms) to send a fetch request when data is not gathered as much as the minimum fetch size setting. | [fetch.max.wait.ms](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.wait.ms) |
| Consumer health check cycle | - | number | Enter a cycle of consumer sending heartbeat. | [heartbeat.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_heartbeat.interval.ms) |
| Metadata update cycle | - | number | Enter the cycle (ms) to update the partition, broker server status, etc. | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms) |
| IDLE timeout | - | number | Enter the wait time (ms) to close a connection without data transmission. | [connections.max.idle.ms](https://kafka.apache.org/documentation/#consumerconfigs_connections.max.idle.ms) |

### Metadata Field Usage

* When the metadata creation is enabled, metadata is created but is not displayed in plugins such as Filter and Sink unless you input data into normal fields.
* Message examples after Kafka plugin when the setting is enabled
```js
{
    // normal fields
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafka topic message..."

    // metadata field
    // Cannot be used until user input data into normal fields
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

## Filter

* Node type that defines how to handle imported data.

## Common Settings on Filter Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Activate Measurement Items  | true | boolean | Collect metrics for nodes.<br/>If Property value is true, you can check the event metric information for node in the Monitoring tab. |  |
| ID | - | string | Sets Node ID<br/>Mark node name on chart board with values defined in this property. |  |
| Add Tag | - | array of string | Add Tag of each message |  |
| Delete Tag | - | array of string | Delete Tag that was given to each message |  |
| Delete Field | - | array of string | Delete Field of each message  |  |
| Add Field | - | Hash | You can add a custom field<br/>You can add fields by calling in the value of each Field with `%{[depth1_field]}`. |  |

## Filter > Alter

### Node Description

* Node that changes message field values to another values.
* You can only change the top-level fields.

### Property Description

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Overwrite Field | - | array of strings | Compare the field value to a given value, if they are equal, modify other field value to the given value.|  |
| Modify Field | - | array of strings | Compare the field value to a given value, if they are equal, modify the field value to the given value. |  |
| Coalesce | - | array of strings | Assign a non-null value to the first of the fields that follow one field. |  |

### Example of Overwrite Field

#### Condition

* Overwrite Field → `["logType", "ERROR", "isBillingTarget", "false"]`

#### Input message

```js
{
    "logType": "ERROR"
}
```

#### Output message

```js
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
| Pattern definition | - | hash | Enter a custom pattern as a regular expression for the rule of tokens to be parsed. | Check the link below for system defined patterns.<br/>http://grokdebug.herokuapp.com/patterns |
| Failure tag | - | array of strings | Enter the tag name to define if string parsing fails. |  |
| Timeout | 30000 | numeric | Enter the amount of time to wait for string parsing. |  |
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
| Timeout | 30000 | numeric | Enter the amount of time to wait for string parsing. |  |
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
| Match | - | array of strings | Enter a field name and format to get strings. |  |
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
    "message": "This messa"
    }
```

## Sink

* Type of node that defines an endpoint to load data that has completed filter operation.

## Common Settings on Sink Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Activate Measurement Items  | true | boolean | Collect metrics for nodes.<br/>If property value is true, you can check the event metric information for node in the monitoring tab. |  |
| ID | - | string | Sets Node ID<br/>Mark node name on the chart board with values defined in this property. |  |

## (NHN Cloud) Object Storage

### Node Description

* Node for uploading data to Object Storage in NHN Cloud.
* Object created on OBS is output in the following path format by default. 
    * `/{container_name}/{yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| region | - | enum | Enter the region of Object Storage product | [OBS Region in Detail](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#aws-sdk) |
| Bucket | - | string | Enter bucket name |  |
| Secret Key | - | string | Enter S3 API Credential Secret Key. |  |
| Access Key | - | string | Enter S3 API Credential Access Key. |  |
| Prefix | /%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | Enter a prefix to prefix the name when uploading the file.<br/>You can enter a field or time format. | [Available Time Format](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | @timestamp | string | Enter a time field to apply to the prefix. |  |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter a time field type to apply to the prefix. |  |
| Prefix Time Zone | UTC | string | Enter a time zone for the Time field to apply to the prefix. |  |
| Prefix Time Application fallback  | _prefix_datetime_parse_failure | string | Enter a prefix to replace if the prefix time application fails. |  |
| Encoding | none | enum | Enter whether to encode or not . gzip encoding is available. |  |
| File Rotation Policy | size_and_time | enum | Determines file creation rules. | size_and_time – Use file size and time to decide<br/>size – Use file size to decide <br/>Time – Use time to decide |
| Standard time | 15 | number | Set the time to be the basis for file splitting.   | Set if file rotation policy is size_and_time or time |
| File size | 5242880 | number | Set the size to be the basis for file splitting.   | Set when file rotation policy is size_and_time or size |
| ACL | private | enum | Enter ACL policy to set when file is uploaded. |  |
| Storage Class | STANDARD | enum | Set Storage Class when file is uploaded. | [ Storage Class Guidel](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |

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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* Output message

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
```

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
| Prefix | - | string | Enter a prefix to prefix the name when uploading the file.<br/>You can enter a field or time format. | [Available Time Format](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | @timestamp | string | Enter a time field to apply to the prefix. |  |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter a time field type to apply to the prefix. |  |
| Prefix Time Zone | UTC | string | Enter a time zone for the Time field to apply to the prefix. |  |
| Prefix Time Application fallback  | _prefix_datetime_parse_failure | string | Enter a prefix to replace if the prefix time application fails. |  |
| Storage Class | STANDARD | enum | Set Storage Class when file is uploaded. | [Storage Class Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| Encoding | none | enum | Enter whether to encode or not . gzip encoding is available. |  |
| File Rotation Policy | size_and_time | enum | Determine file creation rules. | size_and_time – Use file size and time to decide<br/>size – Use file size to decide <br/>Time – Use time to decide |
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

#### force_path_style

* For `true`, the URL must be path-style, not virtual-hosted-style. [Reference](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/VirtualHosting.html)

``` js
{ 
    force_path_style → true 
}
```

## Kafka

### Node Description

* Node for sending data to Kafka.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Topic | - | string | Type the name of Kafka topic to which want to send message. |  |
| Broker Server List | localhost:9092 | string | Enter Kafka Broker server. Separate multiple servers with commas (`, `). | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| Client ID | dataflow | string | Enter ID that identifies Kafka Producer. | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| Message Serialization Type | org.apache.kafka.common.serialization.StringSerializer | string | Enter how to serialize message value to send. | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| Compression type | none | enum | Enter how to compress data to send. | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br/>Select out of none, gzip, snappy, lz4 |
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
| Ack Property  | 1 | enum | Enter settings to verify that messages have been received from Broker server. | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br/>0 - Does not check if 6543eyu0=message is received.<br/>1 - Leader of topic responds that the message was received without waiting for follower to copy data.<br/>all - Leader of topic waits for follower to copy the data before responding that they have received the message. |
| Request Reconnection Cycle | 100 | number | Enter the interval (ms) to retry when transfer request fails. | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms) |
| Retry times | - | number | Enter the maximum times (ms) to retry when transfer request fails. | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br/>Retrying exceeding the set value may result in data loss. |

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
