<!-- pre-align:aligned sig=66678fc2a2b5 -->

<a id="data-analytics-dataflow-codec-configuration-guide"></a>
## Data & Analytics > DataFlow > Codec Configuration Guide { #data-analytics-dataflow-codec-configuration-guide }

<a id="overview"></a>
## Overview { #overview }

* A codec is a component that determines the input and output formats of data.
* Codecs are applied to Source nodes during data ingestion and to Sink nodes during data output.
* We support various codec types, each with its own unique characteristics and use cases.

<a id="supported-codec-type"></a>
## Supported codec type { #supported-codec-type }

<a id="json-codec"></a>
### json codec { #json-codec }

* It parses JSON data to process each field individually.
* Since all JSON fields are preserved intact at both Source and Sink nodes, it is highly effective for data filtering and transformation.

<a id="json-codec-condition"></a>
#### Condition

* Input data

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

* charset → `UTF-8`

<a id="json-codec-process-result"></a>
#### Process result

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

<a id="json-codec-delivery-output-data"></a>
#### Delivery & output data

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

<a id="plain-codec"></a>
### plain codec { #plain-codec }

* Processes raw data in a string format.
* The Source node stores input data as a string in the message field.
* Use this codec when you need to preserve the data in its original form.

<a id="plain-codec-example---source-node"></a>
#### plain codec example - Source node

##### Condition

* Input data
  ```json
  {"name": "DataFlow","type": "pipeline","status": "running","level": "info"}
  ```
* charset → `UTF-8`

##### Process result

```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### Delivery data

```json
{
  "message": "{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"
}
```

<a id="line-codec"></a>
### line codec { #line-codec }

* Processes each line as an individual message.
* The Source node stores each input line as a string in the `message` field, while the Sink node outputs data as text lines according to the specified format.
* Use this codec when line-by-line processing is required.
* For the line codec, you can define a delimiter to separate output messages.
    * The default value is a newline (`\n`).

<a id="line-codec-example---source-node"></a>
#### line codec example - Source node

##### Condition

* Input data
  ```text
  {"name":"DataFlow","type":"pipeline","status":"running","level":"info"}
  ```
* charset → `UTF-8`

##### Process result

```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### Delivery data

```json
{
  "message": "{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"
}
```

<a id="line-codec-example---sink-node"></a>
#### line codec example - Sink node

##### Condition

* Input message
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  }
  ```
* delimiter → `\n`
* format → `[%{level}] %{name} %{status}`
* charset → `UTF-8`

##### Process result

```text
[info] DataFlow running
```

##### Output data

```text
[info] DataFlow running
```

<a id="parquet-codec"></a>
### parquet codec { #parquet-codec }

* Saves data in the Apache Parquet format.
* Supports various compression options and is optimized for large-scale data processing.
* Supported schema types: `string`, `integer`, `long`, `float`, `double`, `boolean`, `timestamp`, `date`
* Supported compression formats: `SNAPPY (default)`, `GZIP`, `LZ4_RAW`, `ZSTD`, `UNCOMPRESSED`
    * [Refer to the compression formats](https://parquet.apache.org/docs/file-format/data-pages/compression/)

<a id="parquet-codec-example---sink-node"></a>
#### parquet Codec Example - Sink Node

##### Condition

* schema
  ```json
  {
    "name": "string",
    "count": "integer", 
    "changeable": "boolean",
    "created":"timestamp"
  }
  ```
* compression type → `SNAPPY`

##### Output Data

Parquet is stored in a column-based binary format and can be viewed using separate tools such as parquet-tools, Apache Spark, and pandas.