## Data & Analytics > DataFlow > Node Configuration Guide

* Node types are pre-defined templates that enable you to easily create flows.
* Node types include Source, Filter, Branch, and Sink.
* For Source and Sink node types, it is recommended to perform a test to verify that the endpoint information is valid.
* When connecting a data source with access control configured, you must use the DataFlow fixed IP feature.
    * To use the DataFlow fixed IP feature, please inquire through **Customer Support > Contact Us**.

### Precautions when connecting Object Storage
When Object Storage services have different regions or projects but share the same bucket name, they cannot be used together in a single flow.

!!! tip "Example of impossible connection configurations"
    * Example 1
        * First Object Storage connection information
            * Region: KR1
            * Bucket name: Data
            * Project: TEST
        * Second Object Storage connection information
            * Region: JP1
            * Bucket name: Data
            * Project: TEST
        * Although the two buckets are different due to their different regions, they cannot be used together in a DataFlow flow
    * Example 2
        * First Object Storage connection information
            * Region: KR1
            * Bucket name: Data
            * Project: TEST_1
        * Second Object Storage connection information
            * Region: KR1
            * Bucket name: Data
            * Project: TEST_2
        * Although the two buckets are different due to their different projects, they cannot be used together in a DataFlow flow

## Domain Specific Language (DSL) Definition

DSL definition required for flow execution.

### Variable

* `{{ executionTime }}`
    * Flow execution time
* Time unit (unit)
    * Minute - `{{ MINUTE }}`
    * Hour - `{{ HOUR }}`
    * Day - `{{ DAY }}`
    * Month - `{{ MONTH }}`
    * Year - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * Returns the start time of the time period defined by `unit` from the given time.
    * [Caution] Calculations are based on Korea Standard Time.
    * Example: \{\{ executionTime \| startOf: MINUTE \}\}
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * Returns the end time of the time period defined by `unit` from the given time.
    * [Caution] Calculations are based on Korea Standard Time.
    * Example: \{\{ executionTime \| endOf: MINUTE \}\}
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * Returns the time after subtracting `delta` `unit`s from the given time.
    * Example: \{\{ executionTime \| subTime: 10, MINUTE \}\}
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * Returns the time after adding `delta` `unit`s to the given time.
    * Example: \{\{ executionTime \| addTime: 10, MINUTE \}\}
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10, MINUTE \}\}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * Returns the given time in the format specified by `formatStr`.
        * iso8601
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
    * Example: \{\{ executionTime \| format: 'yyyy' \}\}
    * Example: \{\{ "2022\-11\-04T13:31:28Z" \| format: 'yyyy' \}\}
        * → 2022
* Nested filter example
    * DSL expression for 03:00 on the day the flow execution started
        * → \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## Input Methods by Data Type

### string

Enter a string.

### number

* Enter a number that is 0 or greater.
* You can adjust the value by 1 using the arrow on the right side of the input field.

### boolean

Select `TRUE` or `FALSE` from the dropdown menu.

### enum

Select an item from the dropdown menu.

### array of strings

* Enter strings for the array one by one.
* Click the `+` button after entering a string to insert it into the array.
* Example: To enter `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`, insert strings into the array in the following order: `message`, `yyyy-MM-dd HH:mm:ssZ`, `ISO8601`.

### hash

Enter a string in JSON format.

## Schema

### Overview

* When you define an output schema (field names and data types) in a Source Node, only the defined fields are selectively read.
* The defined schema is automatically propagated to downstream nodes along the DAG graph.
* When entering fields in a Filter Node, you can select fields defined in the schema from a dropdown.
* If no schema is defined, all fields are read as before.

### Supported Data Types

| Data Type | Description |
|---|---|
| String | String |
| Integer | 32-bit Integer |
| Long | 64-bit Integer |
| Float | 32-bit Floating Point |
| Double | 64-bit Floating Point |
| Boolean | True/False |
| Timestamp | Date and Time |
| Array | Array |

### Schema Definition

* You can define a schema in the **Codec** tab of the Source Node.
* When using the following codecs, you can define a schema in the Source Node.
    * JSON
* PLAIN Codec has data fixed-mapped to the `message` field, so only that field can be defined.
* Build a schema by adding field names and data types.
* When you define a schema, only the defined fields are selectively parsed during flow execution.

### Schema Propagation and Conversion

* The schema defined in the Source Node is automatically propagated to connected downstream nodes.
* The schema is automatically converted based on the properties of the Filter Node.

### Schema-Based Field Selection

* If schema is defined in all upstream Source Nodes, the field list is displayed in a dropdown when entering fields.
* If schema is not defined, fields are entered as text directly as before.

## Source

A node type that defines the endpoint through which data is ingested into the flow.

### Execution Mode

* Source Nodes have an execution mode, divided into BATCH mode and STREAMING mode.
    * STREAMING mode: Data is processed in real-time without terminating the flow.
    * BATCH mode: The flow terminates after processing the defined data.
* Different Source Nodes support different execution modes.

### Common Settings for Source Nodes

| Property | Default | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| ID | - | string | Set the node ID.<br/>The node name is displayed on the dashboard using the value defined in this property. |  |

## Source > (NHN Cloud) Log & Crash Search

### Node Description

* The (NHN Cloud) Log & Crash Search Node reads logs from Log & Crash Search.
* You can set the log query start time in the Node. If not set, logs are read from the time the Flow starts.
* If you do not enter an end time in the Node, logs are read in streaming format. If you enter an end time, logs up to that time are read and the Flow stops.
* Current session logs and crash logs are not supported.
* Its operation depends on the available tokens for Log & Crash Search's log search API.
    * If you have insufficient tokens, contact the Log & Crash Search service.

### Execution Mode
* STREAMING: Continuously processes data after the `Query Start Time`.
* BATCH: Processes all data between `Query Start Time` and `Query End Time`, and stops the Flow.


### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
|-----------|---------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey    | -                   | string | Enter the App Key for Log & Crash Search.                                                                                                                         |    |
| SecretKey | -                   | string | Enter the Secret Key for Log & Crash Search.                                                                                                                       |    |
| Query Start Time  | {{executionTime}} | string | Enter the start time for log query. It must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Query End Time  | -                   | string | Enter the end time for log query. It must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Search Query     | *                   | string | Enter the search query to be used when making a Log & Crash Search query request. For detailed query writing instructions, refer to the 'Lucene Query Guide' in the Log & Crash Search service.                                             |    |

### Message Input by Codec

* Log & Crash Search primarily handles data in JSON format.
* If you want to utilize each field of Log & Crash Search logs, it is recommended to use the JSON Codec.

Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - Parsing JSON format data

## Source > (NHN Cloud) CloudTrail

### Node Description

* (NHN Cloud) CloudTrail is a node that reads data from CloudTrail.
* You can set the data query start time on the node. If not set, data is read from the point when the flow starts.
* If you do not enter an end time on the node, data is read in streaming format. If you enter an end time, data up to that end time is read and the flow ends.

### Execution Mode

* STREAMING: Continuously processes data after the "Query Start Time".
* BATCH: Processes all data between "Query Start Time" and "Query End Time", then ends the flow.

### Property Description

| Property | Default Value | Data Type | Description | Note |
|----------|---------------|-----------|-------------|------|
| Appkey | - | string | Enter the CloudTrail Appkey. | |
| User Access Key ID | - | string | Enter the User Access Key ID for your user account. | |
| Secret Access Key | - | string | Enter the User Secret Key for your user account. | |
| Query Start Time | {{executionTime}} | string | Enter the data query start time. You must enter it in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} | |
| Query End Time | - | string | Enter the data query end time. You must enter it in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>Example: 2025-07-23T11:23:00+09:00, {{ executionTime }} | |
| Event Type | * | string | Enter the event ID to query. | |

### Messages by Codec

* CloudTrail basically handles JSON format data.
* If you want to use each field of CloudTrail data, it is recommended to use JSON Codec.

Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - Parses JSON format data

## Source > (NHN Cloud) Object Storage

### Node Description

* This is a node that receives data input from NHN Cloud Object Storage.
* Data is read starting from the earliest-created object based on object creation time.

### Execution Mode
* STREAMING: The object list is refreshed at each list refresh interval, and newly added objects are read to process data.
* BATCH: The object list is loaded once at the start of the flow, then the objects are read to process data and the flow ends.

### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
| --- |---------| --- | --- | --- |
| Bucket | -       | string | Enter the bucket name to read data from. |  |
| Region | -       | string | Enter the region information configured for the storage. |  |
| Secret Key | -       | string | Enter the secret key credential issued by S3. |  |
| Access Key | -       | string | Enter the access key credential issued by S3. |  |
| List Refresh Interval | 60    | number | Enter the refresh interval for the object list in the bucket. |  |
| Prefix | -       | string | Enter the prefix of the objects to read. |  |
| Excluded Key Pattern | -       | string | Enter the pattern of objects to exclude from reading. |  |

### Messages by Codec

Supported Codecs
* [PLAIN Codec](./codec-config-guide.md#plain) - Store original data as strings
* [JSON Codec](./codec-config-guide.md#json) - Parse JSON-formatted data

## Source > (NHN Cloud) Data Lake Storage

### Node Description
* This is a node that receives data from NHN Cloud's Data Lake Storage.

### Execution Mode
* STREAMING: The object list is refreshed at the interval specified by `list refresh period`, and newly added objects are read to process the data.
* BATCH: The object list is loaded once when the flow starts, objects are read to process the data, and then the flow stops.

### Property Description
| Property Name | Default Value | Data Type | Description | Notes |
| --- |---------| --- | --- | --- |
| Bucket | - | string | Enter the name of the bucket to read data from. | |
| Region | - | string | Enter the region information configured in the storage. | |
| Secret Key | - | string | Enter the secret key credential issued by S3. | |
| Access Key | - | string | Enter the access key credential issued by S3. | |
| List Refresh Period | 60 | number | Enter the interval for refreshing the object list in the bucket. | |
| Prefix | - | string | Enter the prefix of the objects to read. | |
| Exclude Key Pattern | - | string | Enter the pattern of objects to exclude. | |

### Message Input by Codec
Supported Codecs
* [PLAIN Codec](./codec-config-guide.md#plain) - Store original data as a string
* [JSON Codec](./codec-config-guide.md#json) - Parse JSON format data

## Source > (Amazon) S3

### Node Description

* A node that receives data input from S3.
* Data is read starting from the object created earliest based on object creation time.

### Execution Mode

* STREAMING: The object list is updated at each `List Update Interval`, and data is processed by reading newly added objects.
* BATCH: The object list is updated once at the flow start time, then objects are read to process data and the flow terminates.

### Property Description

| Property | Default | Type | Description | Remarks |
|----------|---------|------|-------------|---------|
| Endpoint | - | string | Enter the S3 storage endpoint. | Only HTTP or HTTPS URL format is allowed. |
| Bucket | - | string | Enter the bucket name to read data from. | |
| Region | - | string | Enter the region information configured in the storage. | |
| Secret Key | - | string | Enter the secret key credential issued by S3. | |
| Access Key | - | string | Enter the access key credential issued by S3. | |
| List Update Interval | 60 | number | Enter the update interval for the object list contained in the bucket. | |
| Prefix | - | string | Enter the prefix of the object to read. | |
| Exclude Key Pattern | - | string | Enter the pattern of objects not to read. | |
| Path-Style Request | false | boolean | Determine whether to use path-style requests. | |

!!! danger "Warning"
    * When connecting to NHN Cloud Object Storage using the (Amazon) S3 node, **Path-Style Request** must be set to `true`.

### Message Ingestion by Codec

Supported Codecs
* [PLAIN Codec](./codec-config-guide.md#plain) - Stores original data string
* [JSON Codec](./codec-config-guide.md#json) - Parses JSON format data

## Source > (NHN Cloud) EasyQueue

### Node Description
Receives data from NHN Cloud's EasyQueue.

### Execution Mode
STREAMING: Processes data each time a new message arrives in the queue.

### Properties
| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| App Key | - | string | Enter the App Key for EasyQueue. |  |
| User Access Key ID | - | string | Enter the User Access Key ID of the user account. |  |
| Secret Access Key | - | string | Enter the User Secret Key of the user account. |  |
| Broker Server List | - | string | Enter the Kafka broker server. If there are multiple servers, separate them with a comma (`,`). | Refer to the `bootstrap.servers` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>Example: 10.100.1.1:9092,10.100.1.2:9092 |
| Consumer Group ID | dataflow | string | Enter the ID to identify the Kafka Consumer Group. | Refer to the `group.id` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Topic List | - | array of strings | Enter the list of Kafka topics to receive messages from. |  |
| Topic Pattern | - | string | Enter the Kafka topic pattern to receive messages from. | Example: `*-messages` |
| Exclude Internal Topics | true | boolean | Excludes internal topics such as __consumer_offsets. | Refer to the `exclude.internal.topics` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>Excludes internal topics such as `__consumer_offsets` from message reception targets. |
| Client ID | dataflow | string | Enter the ID to identify the Kafka Consumer. | Refer to the `client.id` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Isolation Level | read_committed | enum | Determines whether the consumer reads uncommitted transaction messages or only committed messages. | Refer to the `isolation.level` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/)<br/>read_uncommitted: Reads all messages in offset order.<br/>read_committed: Reads only messages from committed transactions. |
| Partition Assignment Strategy | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Determines how partitions are assigned to the consumer group when receiving messages from Kafka. | Refer to the `partition.assignment.strategy` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| Offset Configuration | latest | enum | Enter the criteria for setting the offset of the consumer group. | Refer to the `auto.offset.reset` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>All settings below maintain the existing offset if the consumer group already exists.<br/>none: Returns an error if the consumer group does not exist.<br/>earliest: Initializes to the oldest offset of the partition if the consumer group does not exist.<br/>latest: Initializes to the most recent offset of the partition if the consumer group does not exist. |
| Key Deserialization Type | STRING | enum | Enter the type of the key of the messages being received. | Refer to the `key.deserializer` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Create Metadata | false | boolean | When set to true, creates metadata fields for the message. The metadata is created in the `kafka_metadata` field. | The generated fields are as follows.<br/>topic: Topic from which the message was received<br/>groupId: Consumer group ID used to receive the message<br/>partition: Partition number of the topic from which the message was received<br/>offset: Offset of the partition from which the message was received<br/>key: Message key |
| Fetch Minimum Size | 1 | number | Enter the minimum size (byte) of data to fetch in one fetch request. | Refer to the `fetch.min.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Send Buffer Size | 131072 | number | Enter the size (byte) of the TCP send buffer used to transmit data. | Refer to the `send.buffer.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Retry Request Period | 100 | number | Enter the period (ms) to retry when a transmission request fails. | Refer to the `retry.backoff.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Cyclic Redundancy Check | true | boolean | Checks the CRC of the message. | Refer to the `check.crcs` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Server Reconnection Period | 50 | number | Enter the period (ms) to retry when connection to the broker server fails. | Refer to the `reconnect.backoff.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Fetch Size Per Partition | 1048576 | number | Enter the maximum size (byte) of data to fetch in one fetch request per partition. | Refer to the `max.partition.fetch.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Server Request Timeout | 30000 | number | Enter the timeout (ms) for transmission requests. | Refer to the `request.timeout.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| TCP Receive Buffer Size | 65536 | number | Enter the size (byte) of the TCP receive buffer used to read data. | Refer to the `receive.buffer.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Session Timeout | 45000 | number | Enter the session timeout (ms) for the consumer.<br/>If the consumer fails to send a heartbeat within that time, it is removed from the consumer group. | Refer to the `session.timeout.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Poll Messages | 500 | number | Enter the maximum number of messages to fetch in one poll request. | Refer to the `max.poll.records` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Poll Period | 300000 | number | Enter the maximum period (ms) between poll requests. | Refer to the `max.poll.interval.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Fetch Size | 52428800 | number | Enter the maximum size (byte) of data to fetch in one fetch request. | Refer to the `fetch.max.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Fetch Wait Time | 500 | number | Enter the wait time (ms) to send the fetch request if data up to `Fetch Minimum Size` has not been collected. | Refer to the `fetch.max.wait.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Consumer Health Check Period | 3000 | number | Enter the period (ms) at which the consumer sends heartbeats. | Refer to the `heartbeat.interval.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Metadata Update Period | 300000 | number | Enter the period (ms) to update partition and broker server status. | Refer to the `metadata.max.age.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| IDLE Timeout | 540000 | number | Enter the wait time (ms) to close connections with no data transmission. | Refer to the `connections.max.idle.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Additional Settings | - | hash | Enter additional Consumer settings to use for Kafka connection. | Refer to [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |

### Message Ingestion by Codec
Supported Codecs
* [PLAIN Codec](./codec-config-guide.md#plain) - Store original data string
* [JSON Codec](./codec-config-guide.md#json) - Parse JSON format data

## Source > (Apache) Kafka

### Node Description

Receives data from Kafka.

### Execution Mode
STREAMING: Processes data each time a new message arrives in the topic.

!!! danger "Caution"
    * The Kafka node does not support BATCH mode.

### Properties

| Property Name | Default Value | Data Type | Description | Remarks |
|---|---|---|---|---|
| Broker Server List | - | string | Enter Kafka broker servers. If there are multiple servers, separate them with commas (`,`). | See `bootstrap.servers` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>Example: 10.100.1.1:9092,10.100.1.2:9092 |
| Consumer Group ID | dataflow | string | Enter an ID to identify the Kafka Consumer Group. | See `group.id` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Topic List | - | array of strings | Enter a list of Kafka topics to receive messages from. | |
| Topic Pattern | - | string | Enter the Kafka topic pattern to receive messages from. | Example: `*-messages` |
| Exclude Internal Topics | true | boolean | Excludes internal topics such as `__consumer_offsets`. | See `exclude.internal.topics` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>Excludes internal topics such as `__consumer_offsets` from message reception targets. |
| Client ID | dataflow | string | Enter an ID to identify the Kafka Consumer. | See `client.id` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Partition Assignment Strategy | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Determines how partitions are allocated to consumer groups when receiving messages from Kafka. | See `partition.assignment.strategy` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| Offset Settings | latest | enum | Enter the criteria for setting the offset of the consumer group. | See `auto.offset.reset` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>All settings below maintain the existing offset if the consumer group already exists.<br/>none: Returns an error if the consumer group does not exist.<br/>earliest: Initializes to the oldest offset of the partition if the consumer group does not exist.<br/>latest: Initializes to the most recent offset of the partition if the consumer group does not exist. |
| Key Deserialization Type | STRING | enum | Enter the type of the key of the message being received. | See `key.deserializer` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Generate Metadata | false | boolean | If the property value is true, metadata fields for messages are created. Metadata is created in the `kafka_metadata` field. | Generated fields are as follows.<br/>topic: The topic where the message was received<br/>groupId: The consumer group ID used to receive the message<br/>partition: The partition number of the topic where the message was received<br/>offset: The offset of the partition where the message was received<br/>key: The message key |
| Fetch Minimum Size | 1 | number | Enter the minimum size (byte) of data to fetch in one fetch request. | See `fetch.min.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Send Buffer Size | 131072 | number | Enter the size (byte) of the TCP send buffer used to transmit data. | See `send.buffer.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Retry Request Interval | 100 | number | Enter the interval (ms) to retry when a transmission request fails. | See `retry.backoff.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| CRC Check | true | boolean | Checks the CRC of the message. | See `check.crcs` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Server Reconnection Interval | 50 | number | Enter the interval (ms) to retry when a connection to the broker server fails. | See `reconnect.backoff.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Fetch Size per Partition | 1048576 | number | Enter the maximum size (byte) of data to fetch in one fetch request per partition. | See `max.partition.fetch.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Server Request Timeout | 30000 | number | Enter the timeout (ms) for transmission requests. | See `request.timeout.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| TCP Receive Buffer Size | 65536 | number | Enter the size (byte) of the TCP receive buffer used to read data. | See `receive.buffer.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Session Timeout | 45000 | number | Enter the session timeout (ms) for the consumer.<br/>If the consumer fails to send a heartbeat within that time, it will be excluded from the consumer group. | See `session.timeout.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Number of Poll Messages | 500 | number | Enter the maximum number of messages to retrieve in one poll request. | See `max.poll.records` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Maximum Poll Interval | 300000 | number | Enter the maximum interval (ms) between poll requests. | See `max.poll.interval.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Fetch Maximum Size | 52428800 | number | Enter the maximum size (byte) of data to fetch in one fetch request. | See `fetch.max.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Fetch Maximum Wait Time | 500 | number | Enter the waiting time (ms) to send a fetch request when data does not accumulate as much as the `Fetch Minimum Size` setting. | See `fetch.max.wait.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Consumer Health Check Interval | 3000 | number | Enter the interval (ms) at which the consumer sends heartbeats. | See `heartbeat.interval.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Metadata Update Interval | 300000 | number | Enter the interval (ms) to update partition and broker server status, etc. | See `metadata.max.age.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| IDLE Timeout | 540000 | number | Enter the waiting time (ms) to close connections with no data transmission. | See `connections.max.idle.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Isolation Level | read_committed | enum | Determines whether the consumer reads uncommitted transaction messages or only committed messages. | See `isolation.level` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/)<br/>read_uncommitted: Reads all messages in offset order.<br/>read_committed: Reads only messages from committed transactions. |
| Additional Configuration | - | hash | Enter additional Consumer configuration to use for Kafka connections. | See [Kafka Official Documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |

### Messages by Codec

Supported Codecs
* [PLAIN Codec](./codec-config-guide.md#plain) - Store original data string
* [JSON Codec](./codec-config-guide.md#json) - Parse JSON format data

## Filter

A node type that defines how to process incoming data.

### Common Filter Node Configuration

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| ID | - | string | Sets the node's ID.<br/>The node name is displayed on the dashboard using the value defined in this property. |  |

## Filter > Cipher

### Node Description

* A node that encrypts and decrypts message field values.
* The encryption key refers to Secure Key Manager symmetric keys.
    * Secure Key Manager symmetric keys can be created via Secure Key Manager web console or Secure Key Manager's key addition API.
    * Even if multiple Cipher nodes are included in one flow, all Cipher nodes must refer to exactly one Secure Key Manager key reference.

### Property Description

| Property Name    | Default Value     | Data Type     | Description                                        | Notes               |
|--------|---------|---------|-------------------------------------------|------------------|
| Mode     | -       | enum    | Select between encryption mode and decryption mode.                   | Select one from the list. |
| App Key     | -       | string  | Enter the SKM app key that stores the key to be used for encryption/decryption.          |                  |
| Key ID   | -       | string  | Enter the SKM key ID that stores the key to be used for encryption/decryption.        |                  |
| Key Version   | -       | string  | Enter the SKM key version that stores the key to be used for encryption/decryption.        |                  |
| Source Field  | -       | string  | Enter the field name to encrypt/decrypt.                        | Dropdown provided when defining schema |
| Target Field | -       | string  | Enter the field name where the encryption/decryption result will be saved.                 |                  |
| Overwrite   | false | boolean | Select whether to overwrite if a value already exists in the specified target field. |                  |

### encrypt Example

#### Conditions

* mode → `encrypt`
* App Key → `SKM app key`
* Key ID → `SKM symmetric key ID`
* Key Version → `1`
* Source Field → message
* Target Field → encrypted\_message

#### Input Message

``` js
{
    "message": "this is plain message"
}
```

#### Output Message

``` js
{
    "message": "this is plain message",
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

### decrypt Example

#### Conditions

* mode → `decrypt`
* App Key → `SKM app key`
* Key ID → `SKM symmetric key ID`
* Key Version → `1`
* Source Field → message
* Target Field → decrypted\_message

#### Input Message

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

#### Output Message

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e",
    "decrypted_message": "this is plain message"
}
```

## Filter > CSV

### Node Description
A node that parses CSV format messages and stores the results in fields.

### Properties Description

| Property Name | Default Value | Data Type | Description | Notes |
|---|---|---|---|---|
| Field to Save | - | string | Enter the field name to store the CSV parsing result. | |
| Quote | " | string | Enter the character that encloses column fields. | |
| Skip First Row | false | boolean | If the property value is true, column names in the first row of the read data are ignored. | |
| Delimiter | , | string | Enter the string to separate columns. | |
| Source Field | - | string | Enter the field name to parse CSV from. | |
| Schema | - | hash | Enter the name and data type of each column in dictionary format. | See `Schema Input Method` |
| Overwrite | false | boolean | If true, CSV parsing results overwrite the field to save or existing fields if they overlap. | |
| Delete Original Field | false | boolean | Deletes the source field after CSV parsing is complete. If parsing fails, it is retained. | |

#### Schema Input Method
Does not support individual column types; accepts the entire columns and data types as schema input.


### CSV Parsing Example Without Data Type Conversion

#### Conditions

* Source Field → `message`
* Schema → `{"one": "string", "two": "string", "t hree": "string"}`

#### Input Message

```js
{
    "message": "hey,foo,\"bar baz\""
}
```

#### Output Message

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```


### CSV Parsing Example With Data Type Conversion

#### Conditions

* Source Field → `message`
* Schema → `{"one": "string", "two": "integer", "t hree": "boolean"}`

#### Input Message

```js
{
    "message": "\"wow hello world!\", 2, false"
}
```

#### Output Message

```js
{
    "message": "\"wow hello world!\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### Node Description

This node parses JSON strings and stores them in the designated field.

### Properties

| Property Name | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Source Field | - | string | Enter the field name containing the JSON string to parse. |  |
| Storage Field | - | string | Enter the field name to store the JSON parsing result.<br/>If no value is specified, the result is stored in the root field. |  |
| Overwrite | false | boolean | When set to true, overwrites the field if the JSON parsing result overlaps with the storage field or existing field. |  |
| Delete Source Field | false | boolean | Deletes the source field when JSON parsing is complete. If parsing fails, the field is retained. |  |
| Schema | - | hash | Enter the name and data type of each field in dictionary format. | Refer to `Schema Input Method` |

#### Schema Input Method

Column types are not supported; enter all columns and data types as a schema.

### JSON Parsing Example Without Data Type Conversion

#### Conditions

* Source Field → `message`
* Storage Field → `json_parsed_message`

#### Input Message

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

#### Output Message

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": "string"
    },
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

### JSON Parsing Example Requiring Data Type Conversion

#### Conditions

* Source Field → `message`
* Storage Field → `json_parsed_message`
* Schema → `{"json": "string", "example": "integer"}`

#### Input Message

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"123\"}"
}
```

#### Output Message

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": 123
    },
    "message": "{\"json\": \"parse\", \"example\": \"123\"}"
}
```

## Filter > Date

### Node Description

A node that parses Date strings and stores them in timestamp format.

### Attribute Description

| Property | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Source Field | - | string | Enter the field name to retrieve the string. | Dropdown provided when defining the schema |
| Format | - | array of strings | Enter the format to retrieve the string. | The predefined formats are as follows.<br/>ISO8601, UNIX, UNIX_MS |
| Locale | ko_KR | string | Enter the Locale to use for parsing Date strings. | Example: en, en-US, ko_KR |
| Field to Save | - | string | Enter the field name to save the Date string parsing result. | |
| Timezone | Asia/Seoul | string | Enter the timezone for the date. | Example: Asia/Seoul |

### Date String Parsing Example

#### Conditions

* Source Field → `message`
* Format → `["yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* Field to Save → `time`
* Timezone → `Asia/Seoul`

#### Input Message

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### Output Message

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### Node Description

A node that generates a UUID and stores it in a field.

### Attribute Description

| Property | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Field to Store UUID | - | string | Enter the field name to store the UUID generation result. | |
| Overwrite | false | boolean | Select whether to overwrite the existing value if a value exists in the specified field name. | |

### UUID Generation Example

#### Conditions

Field to Store UUID → `userId`

#### Input Message

```js
{
    "message": "uuid test message"
}
```

#### Output Message

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Convert

### Node Description

A node that converts the data type of a specific field.

### Properties

| Property Name | Default Value | Data Type | Description | Remarks |
|-------|-----|--------|-----------------------------------------------------------------------------|----|
| Target Field | - | string | Enter the target field to convert the data type. | Dropdown provided when defining schema |
| Conversion Type | - | enum | Select the data type to convert. <br/> * Provided types: `STRING, INTEGER, FLOAT, DOUBLE, BOOLEAN` | |

### Data Conversion Example

#### Conditions

* Target Field → `message`
* Conversion Type → `INTEGER`

#### Input Message

```js
{
    "message": "2025"
}
```

#### Output Message

```js
{
    "message": 2025
}
```

## Filter > Coerce

### Node Description

A node that replaces null values with default values.

### Properties

| Property Name | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Target Field | - | string | Enter the field name to specify the default value. | Dropdown provided when defining schema |
| Default Value | - | string | Enter the default value. | |

### Default Value Configuration Example

#### Conditions
* Target Field → `fieldname`
* Default Value → `default_value`

#### Input Message

```json
{
    "fieldname": null
}
```

#### Output Message

```json
{
    "fieldname": "default_value"
}
```

## Filter > Copy

### Node Description

A node that copies an existing field to another field.

### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Source Field | - | string | Enter the source field name to copy. | Dropdown provided when defining schema |
| Target Field | - | string | Enter the field name to store the copied result. |  |
| Overwrite | false | boolean | If true, overwrites the target field if it already exists. |  |

### Example

#### Condition
* Source Field → `source_field`
* Target Field → `dest_field`

#### Input Message

```json
{
    "source_field": "Hello World!"
}

```

#### Output Message

```json
{
    "source_field": "Hello World!",
    "dest_field": "Hello World!"
}
```

## Filter > Rename

### Node Description

A node that renames a field.

### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Source Field |  | string | Enter the source field to rename. | Dropdown provided when defining schema |
| Target Field | - | string | Enter the field name to change to. |  |
| Overwrite | false | boolean | If true, overwrites the target field if it already exists. |  |

### Example

#### Condition
* Source Field → `fieldname`
* Target Field → `changed_fieldname`

#### Input Message

```json
{
    "fieldname": "Hello World!"
}
```

#### Output Message

```json
{
    "changed_fieldname": "Hello World!"
}
```

## Filter > Strip

### Node Description

A node that removes leading and trailing whitespace from string fields.

### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Target Field | - | array of strings | Enter the fields to remove whitespace from. | Dropdown provided when defining schema (multiple selection) |

### Example

#### Condition
Target Field → `["field1", "field2"]`

#### Input Message

```json
{
    "field1": "Hello World!   ",
    "field2": "   Hello DataFlow!"
}

```

#### Output Message

```json
{
    "field1": "Hello World!",
    "field2": "Hello DataFlow!"
}

```

## Filter > Remove Fields

### Node Description

A node that deletes fields.

### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
|--------|-----|------------------|--------------------|----|
| Fields to Delete | - | array of strings | Enter the list of field names to delete. | Dropdown provided when defining schema (multiple selection) |

### Configuration Example

#### Condition
Fields to Delete → `["field2", "field3"]`

#### Input Message

```json
{
    "field1": "value1",
    "field2": "value2",
    "field3": "value3",
    "field4": "value4"
}
```

#### Output Message

```json
{
    "field1": "value1",
    "field4": "value4"
}
```

## Filter > Tokenizer

### Node Description

A node that tokenizes string fields using regular expressions.

### Attributes

| Property | Default Value | Data Type | Description | Notes |
|----------|---|---|---|---|
| Source Field | - | string | Enter the name of the source field to tokenize. | |
| Target Field | - | string | Enter the name of the field to store the tokenization result. | |
| Regular Expression | \s+ | string | Enter the regular expression to be used for tokenization. | |
| Mode | SEPARATOR | enum | Select the tokenization mode. | SEPARATOR: Use regular expression as a separator<br>MATCH: Use regular expression for token matching |
| Minimum Token Length | 1 | number | Enter the minimum length of tokens. Tokens shorter than the minimum token length are excluded from the results. | |
| Overwrite | false | boolean | If true, the target field is overwritten if it already exists. | |

### SEPARATOR Mode Example

#### Conditions
* Source Field → `src_field`
* Target Field → `target_field`
* Regular Expression → `,`
* Mode → `SEPARATOR`

#### Input Message

```json
{
    "src_field": "foo,bar,baz"
}
```

#### Output Message

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

### MATCH Mode Example

#### Conditions
* Source Field → `src_field`
* Target Field → `target_field`
* Regular Expression → `[^,]+`
* Mode → `MATCH`

#### Input Message

```json
{
    "src_field": "foo,bar,baz"
}
```

#### Output Message

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

## Filter > Sampling

### Node Description

* A node that selects messages at a certain ratio and passes them to the next node.
* Whether to pass is determined based on probability. Therefore, the smaller the number of messages, the larger the error from the entered ratio.

### Attributes

| Property | Default Value | Data Type | Description | Notes |
|---|---|---|---|---|
| Ratio | - | number | Enter the ratio to pass messages to the next node. | |
| Seed | - | number | Enter the seed to use when generating random numbers. If the seed is the same and the input messages are identical, the results will be identical. | |

## Filter > Stop Words Remover

### Node Description

A node that removes stop words contained in a string array field.

### Properties

| Property | Default | Data Type | Description | Notes |
|----------|---------|-----------|-------------|-------|
| Source Field | - | string | Enter the source field name to remove stop words from. | |
| Target Field | - | string | Enter the field name to save the stop word removal result. | |
| Built-in Stop Words Dictionary Language | none | enum | Select the language of the built-in stop words dictionary to use for stop word removal. | |
| Stop Words Dictionary | | string | Enter the word list to use for stop word removal. Each word is separated by a line break. | |
| Case Sensitive | false | boolean | Select whether to distinguish between uppercase and lowercase letters. | |
| Overwrite | false | boolean | If true, overwrites the target field if it already exists. | |

### Predefined Dictionaries

* Predefined dictionaries by language are as follows:
  * [ko](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_ko.txt)
  * [en](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_en.txt)

### Configuration Example

#### Conditions

* Source Field → `src_field`
* Target Field → `target_field`
* Dictionary
```
is
a
```

#### Input Message

```json
{
    "src_field": ["hello", "world", "this", "is", "a", "test"]
}
```

#### Output Message

```json
{
  "src_field": ["hello", "world", "this", "is", "a", "test"],
  "target_field": ["hello", "world", "this", "test"]
}
```

## Filter > Pattern Extractor (Grok)

### Node Description

* Node that extracts structured information from text data.
* Extracts necessary information from logs or text through pattern matching using regular expressions.
* Supports Grok pattern syntax compatible with Logstash, enabling simple pattern-based handling of complex log parsing.
* Can parse data in various formats by utilizing predefined patterns or creating custom patterns.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
|---|---|---|---|---|
| Source Field | - | string | Enter the source field name from which to extract the pattern. |   |
| Target Field | - | string | Enter the field name to store the extraction result. If not specified, the result is added directly to root. |   |
| Custom Pattern | - | hash | Define additional patterns to use beyond predefined patterns. Enter pattern names and regular expressions in key-value format. | If a pattern name already exists in predefined patterns, the custom pattern takes precedence and can override the predefined pattern. |
| Pattern Expression | - | string | Enter fields to extract from data and patterns as Grok expressions. |   |
| Overwrite | false | boolean | Set whether to overwrite extraction results if a value already exists in the target field. |   |

!!! tip "Predefined Patterns"
    Provides predefined patterns for frequently used scenarios.
    Includes various patterns needed for different situations such as date/time, IP addresses, URLs, and log levels.
    Predefined patterns have a hierarchical structure that references other patterns internally, so additional fields may be created beyond the specified field names.
    See [Predefined Pattern List](https://static.toastoven.net/prod_dataflow/node-config-guide/predefined_patterns.txt)

### Example

#### Conditions
* Source Field → `log_message`
* Target Field → `result`
* Custom Pattern → `{"CUSTOM_PHONE_NUMBER": "01[016789]-\d{3,4}-\d{4}", "CUSTOM_EMPLOYEE_ID": "EMP-\d{6}", "CUSTOM_ORDER_ID": "ORD-[A-Z]{3}-\d{8}"}`
* Pattern Expression → `%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{CUSTOM_EMPLOYEE_ID:custom_emp_id} %{CUSTOM_PHONE_NUMBER:custom_phone_number} %{CUSTOM_ORDER_ID:custom_order_id} %{GREEDYDATA:message}`

#### Input Message

```json
{
  "log_message": "2024-03-15T09:30:00.000Z INFO EMP-123456 010-1234-5678 ORD-ABC-12345678 Order processing started",
  "created_by": "DataFlow"
}

```

#### Output Message

```json
{
  "log_message": "2024-03-15T09:30:00.000Z INFO EMP-123456 010-1234-5678 ORD-ABC-12345678 Order processing started",
  "created_by": "DataFlow",
  "result": {
    "YEAR": "2024",
    "MONTHNUM": "03",
    "ISO8601_TIMEZONE": "Z",
    "MONTHDAY": "15",
    "HOUR": [
      "09",
      null
    ],
    "MINUTE": [
      "30",
      null
    ],
    "SECOND": "00.000",
    "timestamp": "2024-03-15T09:30:00.000Z",
    "level": "INFO",
    "custom_emp_id": "EMP-123456",
    "custom_phone_number": "010-1234-5678",
    "custom_order_id": "ORD-ABC-12345678",
    "message": "Order processing started"
  }
}
```

## Sink

Node type that defines the endpoint where data processed through Filter operations is loaded.

### Common Settings for Sink Node

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| ID | - | string | Set the ID of the node.<br/>The node name is displayed on the dashboard with the value defined in this property. |  |

## Sink > (NHN Cloud) Object Storage

### Node Description

* A node that uploads data to NHN Cloud's Object Storage.
* If you create it with only the default settings without any additional configuration, the object will be output according to the following path format:
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* The supported codecs are JSON, LINE, and Parquet.

### Properties

| Property Name | Default Value | Data Type | Description | Note |
|-----------------------|----------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Region | - | enum | Enter the region of the Object Storage service. | |
| Bucket | - | string | Enter the bucket name. | |
| Secret Key | - | string | Enter the S3 API credential secret key. | |
| Access Key | - | string | Enter the S3 API credential access key. | |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | Enter the prefix to be added before the object name when uploading.<br/>You can enter field or time format. | [Available time formats](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | - | string | Enter the time field to apply to the Prefix. | |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter the type of the time field to apply to the Prefix. | Only DATE_FILTER_RESULT type is supported (other types will be supported in the future) |
| Prefix Time Zone | UTC | string | Enter the time zone of the time field to apply to the Prefix. | |
| Prefix Time Application Fallback | _prefix_datetime_parse_failure | string | Enter the Prefix to be used as a fallback when Prefix time application fails. | |
| Base Time | 1 | number | Set the standard time to split the object. | |
| Base Object Size | 5242880 | number | Set the standard size (unit: byte) to split the object. | |
| Inactivity Interval | 1 | number | Set the standard time to split the object when there is no data input for a continuous period. | If there is no data input during the set time, the current object is uploaded, and subsequently newly incoming data will be written to a new object. |

### Output Examples by Codec

Supported codecs
* [JSON Codec](./codec-config-guide.md#json) - Parsing JSON format data
* [LINE Codec](./codec-config-guide.md#line) - Processing messages by line
* [Parquet Codec](./codec-config-guide.md#parquet) - Compressing data to Parquet format

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
/obs-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - Time

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
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - When Time Application Fails

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix Time Field → `logTime`
* Prefix Time Field Type → `TIMESTAMP_SEC`
* Prefix Time Zone → `Asia/Seoul`
* Prefix Time Application Fallback → `_failure`

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
/obs-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (NHN Cloud) Data Lake Storage

### Node Description
* A node that uploads data to NHN Cloud's Data Lake Storage.
* When created with only default settings without additional configuration, objects are output in the following path format.
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* Supported codecs are JSON, LINE, and Parquet.

### Attributes
| Property Name | Default Value | Data Type | Description | Notes |
|-----------------------|----------------------------------------------------|--------|------|-----|
| Region | - | enum | Enter the region of the Data Lake Storage product. | |
| Bucket | - | string | Enter the bucket name. | |
| Secret Key | - | string | Enter the S3 API credential secret key. | |
| Access Key | - | string | Enter the S3 API credential access key. | |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | Enter the prefix to prepend to the object name when uploading.<br/>You can enter a field or time format. | [Available time formats](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | - | string | Enter the time field to apply to the Prefix. | |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter the type of the time field to apply to the Prefix. | Only DATE_FILTER_RESULT type is available (other types may be supported in the future) |
| Prefix Time Zone | UTC | string | Enter the time zone of the time field to apply to the Prefix. | |
| Prefix Time Application Fallback | _prefix_datetime_parse_failure | string | Enter the Prefix to use when Prefix time application fails. | |
| Reference Time | 1 | number | Set the time to be used as the reference for splitting objects. | |
| Reference Object Size | 5242880 | number | Set the size (unit: byte) to be used as the reference for splitting objects. | |
| Inactivity Interval | 1 | number | Set the reference time for splitting objects when no data is being ingested. | If no data is ingested during the set time, the current object is uploaded, and subsequent incoming data will be written to a new object. |

### Output Examples by Codec
Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - Parsing JSON format data
* [LINE Codec](./codec-config-guide.md#line) - Processing messages by line
* [Parquet Codec](./codec-config-guide.md#parquet) - Compressing data to Parquet format 

### Prefix Example - Field
#### Conditions
* Bucket → `dls-test-container`
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
/dls-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - Time
#### Conditions
* Bucket → `dls-test-container`
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
/dls-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - Time Application Failure
#### Conditions
* Bucket → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix Time Field → `logTime`
* Prefix Time Field Type → `ISO8601`
* Prefix Time Zone → `Asia/Seoul`
* Prefix Time Application Fallback → `_failure`

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
/dls-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (Amazon) S3

### Node Description

* A node that uploads data to Amazon S3.
* Supported codecs are JSON, LINE, and Parquet.

### Attribute Descriptions
| Property | Default | Type | Description | Notes |
| --- | --- | --- | --- | --- |
| Region | - | enum | Enter the region for the S3 product. | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| Bucket | - | string | Enter the bucket name. |  |
| Access Key | - | string | Enter the access key for S3 API credentials. |  |
| Secret Key | - | string | Enter the secret key for S3 API credentials. |  |
| Prefix | - | string | Enter the prefix to prepend to the object name when uploading.<br/>You can enter field or time format. | [Available time formats](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | - | string | Enter the time field to apply to the Prefix. |  |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter the type of time field to apply to the Prefix. | Only DATE_FILTER_RESULT type is supported (other types will be supported in the future) |
| Prefix Timezone | UTC | string | Enter the timezone for the time field to apply to the Prefix. |  |
| Prefix Time Application Fallback | _prefix_datetime_parse_failure | string | Enter the fallback Prefix to use if Prefix time application fails. |  |
| Reference Time | 1 | number | Set the time that will serve as the standard for splitting objects. |  |
| Reference Object Size | 5242880 | number | Set the size that will serve as the standard for splitting objects. |  |
| Path-Style Request | false | boolean | Determine whether to use path-style requests. |  |
| Inactivity Interval | 1 | number | Set the reference time for splitting objects when there is no data inflow. | If no data is received during the set time period, the current object is uploaded, and any new data received afterward is written to a new object. |

!!! danger "Caution"
    * When connecting to NHN Cloud Object Storage using the (Amazon) S3 node, you must set **path-style request** to `true`.


### Output Examples by Codec

Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - JSON format data parsing
* [LINE Codec](./codec-config-guide.md#line) - Line-unit message processing
* [Parquet Codec](./codec-config-guide.md#parquet) - Compress data in Parquet format

## Sink > (NHN Cloud) EasyQueue

### Node Description
A node that sends data to NHN Cloud's EasyQueue.

### Property Description
| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| App Key | - | string | Enter the app key of EasyQueue. | |
| User Access Key ID | - | string | Enter the User Access Key ID of your user account. | |
| Secret Access Key | - | string | Enter the User Secret Key of your user account. | |
| Topic | - | string | Enter the Kafka topic name to send messages to. | |
| Broker Server List | - | string | Enter the Kafka broker servers. If there are multiple servers, separate them with a comma (`,`). | Refer to the `bootstrap.servers` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>Example: 10.100.1.1:9092,10.100.1.2:9092 |
| Client ID | dataflow | string | Enter the ID to identify the Kafka Producer. | Refer to the `client.id` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Compression Type | none | enum | Enter the method to compress the data being sent. | Refer to the `compression.type` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/topic-level-configs/)<br/>Choose from: none, gzip, snappy, lz4, zstd |
| Message Key | - | string | Enter the field to use as the message key. | |
| Metadata Update Interval | 300000 | number | Enter the interval (ms) to update partition and broker server status, etc. | Refer to the `metadata.max.age.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Max Request Size | 1048576 | number | Enter the maximum size (bytes) per send request. | Refer to the `max.request.size` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Server Reconnection Interval | 50 | number | Enter the interval (ms) to retry when the connection to the broker server fails. | Refer to the `reconnect.backoff.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Batch Size | 16384 | number | Enter the size (bytes) to send in batch request. | Refer to the `batch.size` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Buffer Memory | 33554432 | number | Enter the size (bytes) of the buffer used for Kafka transmission. | Refer to the `buffer.memory` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Receive Buffer Size | 32768 | number | Enter the size (bytes) of the TCP receive buffer used to read data. | Refer to the `receive.buffer.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Send Delay | 0 | number | Enter the time to delay message transmission. Delayed messages are sent in batch request. | Refer to the `linger.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Server Request Timeout | 30000 | number | Enter the timeout (ms) for the send request. | Refer to the `request.timeout.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Send Buffer Size | 131072 | number | Enter the size (bytes) of the TCP send buffer used to transmit data. | Refer to the `send.buffer.bytes` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| ack Property | all | enum | Enter the setting to confirm whether the broker server has received the message. | Refer to the `acks` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>0 - Do not confirm whether the message was received.<br/>1 - The leader of the topic responds that the message was received without waiting for followers to replicate the data.<br/>all - The leader of the topic responds that the message was received after waiting for followers to replicate the data. |
| Retry Request Interval | 100 | number | Enter the interval (ms) to retry when the send request fails. | Refer to the `retry.backoff.ms` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Retry Count | 2147483647 | number | Enter the maximum number of retries when the send request fails. | Refer to the `retries` property in [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>If retries exceed the configured value, data loss may occur. |
| Message Delivery Guarantee | EXACTLY_ONCE | enum | Select the message delivery guarantee method. | AT_LEAST_ONCE: The message is delivered at least once, but duplication may occur in case of failure. Suitable for cases where the application can directly manage duplicate processing or where duplication is allowed.<br/><br/>EXACTLY_ONCE: The message is processed exactly once. Suitable for critical transactions such as payment and settlement where duplication is not allowed, but throughput may be somewhat lower due to internal use of transactions. |
| Additional Settings | - | hash | Enter additional Producer settings for Kafka connections. | Refer to [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |

### Output Examples by Codec
Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - Parsing JSON format data
* [LINE Codec](./codec-config-guide.md#line) - Line-based message processing

## Sink > (Apache) Kafka

### Node Description

A node that transmits data to Kafka.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
|---|---|---|---|---|
| Topic | - | string | Enter the name of the Kafka topic to transmit messages to. | |
| Broker Server List | | string | Enter the Kafka broker server. If there are multiple servers, separate them with commas (`,`). | See the `bootstrap.servers` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>Example: 10.100.1.1:9092,10.100.1.2:9092 |
| Client ID | dataflow | string | Enter the ID that identifies the Kafka Producer. | See the `client.id` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Compression Type | none | enum | Enter the method to compress the data being transmitted. | See the `compression.type` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/topic-level-configs/)<br/>Choose from: none, gzip, snappy, lz4, zstd |
| Message Key | - | string | Enter the field to be used as the message key. | |
| Metadata Refresh Interval | 300000 | number | Enter the interval (ms) to refresh partition and broker server status, etc. | See the `metadata.max.age.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Maximum Request Size | 1048576 | number | Enter the maximum size (byte) per transmission request. | See the `max.request.size` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Server Reconnection Interval | 50 | number | Enter the interval (ms) to retry when connection to the broker server fails. | See the `reconnect.backoff.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Batch Size | 16384 | number | Enter the size (byte) to transmit with a batch request. | See the `batch.size` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Buffer Memory | 33554432 | number | Enter the size (byte) of the buffer used for Kafka transmission. | See the `buffer.memory` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Receive Buffer Size | 32768 | number | Enter the size (byte) of the TCP receive buffer used to read data. | See the `receive.buffer.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Send Delay Time | 0 | number | Enter the time to delay message transmission. Delayed messages are transmitted at once via a batch request. | See the `linger.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Server Request Timeout | 30000 | number | Enter the timeout (ms) for a transmission request. | See the `request.timeout.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Send Buffer Size | 131072 | number | Enter the size (byte) of the TCP send buffer used to transmit data. | See the `send.buffer.bytes` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| ack Property | all | enum | Enter the setting to verify whether the broker server received the message. | See the `acks` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>0 - Does not verify whether the message was received.<br/>1 - The topic leader responds that the message was received without waiting for the follower to copy the data.<br/>all - The topic leader responds that the message was received after waiting for the follower to copy the data. |
| Retry Request Interval | 100 | number | Enter the interval (ms) to retry when a transmission request fails. | See the `retry.backoff.ms` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Retry Count | 2147483647 | number | Enter the maximum number of retries when a transmission request fails. | See the `retries` property in [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>If retries exceed the set value, data loss may occur. |
| Message Delivery Guarantee | EXACTLY_ONCE | enum | Select the message delivery guarantee method. | AT_LEAST_ONCE: Messages are delivered at least once, but duplicates may occur in case of failure. Suitable for cases where duplicate handling can be managed directly by the application or duplicates are acceptable.<br/>EXACTLY_ONCE: Messages are processed exactly once. Suitable for critical transactions such as payment and settlement where duplicates are not allowed, but throughput may be slightly lower due to internal transaction usage. |
| Additional Settings | - | hash | Enter additional Producer settings for Kafka connection. | See [Kafka Official Documentation](https://kafka.apache.org/39/configuration/producer-configs/) |

### Output Example by Codec

Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - JSON format data output
* [LINE Codec](./codec-config-guide.md#line) - Line-based message output

## Sink > Stdout

### Node Description

* A node that outputs messages to standard output.
* Useful for checking data processed from Source and Filter nodes.

### Output Example by Codec

Supported Codecs
* [JSON Codec](./codec-config-guide.md#json) - JSON format data output
* [LINE Codec](./codec-config-guide.md#line) - Line-based message output

## Branch

A node type that defines flow branching based on the value of incoming data.

## Branch > IF

### Node Description

A node that filters messages using conditional statements.

### Property Description

| Property Name | Default Value | Data Type | Description | Remarks |
| --- | --- | --- | --- | --- |
| Condition | - | string | Enter the condition to filter messages. | See the examples below. |

#### Available Operators
* Comparison: ==, !=, <, >, <=, >=
* Regular Expression: =~ (checks the string on the left against the pattern given on the right)
* Contains: =~, !~, .contains()
* Logical Operators: &&, ||, not
* Negation Operators: !, not

### Filtering Example - first depth field reference

#### Condition
Condition → `logLevel == "ERROR"`

#### Passing Message

``` json
{
    "logLevel": "ERROR"
}
```

#### Filtered Out Message

``` json
{
    "logLevel": "INFO"
}
```

### Filtering Example - second depth field reference

#### Condition

Condition → `response.status == 200` or `response["status"] == 200`

#### Passing Message

``` json
{
    "response": {
        "status": 200
    }
}
```

#### Filtered Out Message

``` json
{
    "response": {
        "status": 404
    }
}
```

## Branch > Dataset Split

### Node Description

* A node that splits events into multiple branches according to a configured ratio.
* Can be utilized for purposes such as machine learning dataset splitting (e.g., training/test/validation).
* One child node can be connected to each branch.

### Property Description

| Property Name | Default Value | Data Type | Description | Note |
| --- | --- | --- | --- | --- |
| Seed | - | number | Enter the seed to use when generating random numbers. If the seed is the same and input messages are identical, the results will be the same. |  |
| Split Configuration | - | hash | Enter the branch name and ratio in JSON format. The sum of all ratios must be `1.0`. | Example: `{"train": 0.6, "test": 0.3, "sampling": 0.1}` |

### Event Split Example

#### Conditions

* Seed → `42`
* Split Configuration → `{"train": 0.6, "test": 0.3, "sampling": 0.1}`

#### Operation

Input events are delivered to each branch according to the configured ratio.

* Child node connected to `train` branch: Approximately 60% of all events are delivered.
* Child node connected to `test` branch: Approximately 30% of all events are delivered.
* Child node connected to `sampling` branch: Approximately 10% of all events are delivered.