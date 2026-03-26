## Data & Analytics > DataFlow > Codec Configuration Guide

## Overview

* A codec is a component that determines the input and output formats of data.
* Codecs are applied to Source nodes during data ingestion and to Sink nodes during data output.
* We support various codec types, each with its own unique characteristics and use cases.

## Supported codec type

### JSON codec

* It parses JSON data to process each field individually.
* Since all JSON fields are preserved intact at both Source and Sink nodes, it is highly effective for data filtering and transformation.

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

#### Process result

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

#### Delivery & output data

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

### plain codec

* Processes raw data in a string format.
* The Source node stores input data as a string in the message field.
* Use this codec when you need to preserve the data in its original form.

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

### line codec

* Processes each line as an individual message.
* The Source node stores each input line as a string in the `message` field, while the Sink node outputs data as text lines according to the specified format.
* Use this codec when line-by-line processing is required.
* For the Line codec, you can define a delimiter to separate output messages.
    * The default value is a newline (`\n`).

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

### parquet codec

* Saves data in the Apache Parquet format.
* Supports various compression options and is optimized for large-scale data processing.
* Supported schema types: `string`, `integer`, `long`, `float`, `double`, `boolean`, `timestamp`, `date`
* Supported compression formats: `SNAPPY (default)`, `GZIP`, `LZ4_RAW`, `ZSTD`, `UNCOMPRESSED`
    * [Refer to the compression formats](https://parquet.apache.org/docs/file-format/data-pages/compression/)

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