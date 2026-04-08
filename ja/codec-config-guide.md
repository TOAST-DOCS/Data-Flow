## Data & Analytics > DataFlow > コーデック設定ガイド

## 概要

* コーデックはデータの入出力形式を決定する構成要素です。
* Sourceノードではデータ流入時、Sinkノードではデータ出力時にコーデックが適用されます。
* 多様なコーデックタイプに対応しており、各コーデックごとに固有の特性と使用例があります。

## 対応コーデックタイプ

### JSONコーデック

* JSON形式のデータをパースして各フィールドを個別に処理します。
* Source及びSinkノードともにJSONの全てのフィールドがそのまま維持されるため、フィルタリングや加工に有利です。

#### 条件

* 入力データ

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```
* charset → `UTF-8`

#### 処理結果 

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

#### 伝達&出力データ

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

### plainコーデック

* 元データを文字列形式で処理します。
* Sourceノードで入力データを`message`フィールドに文字列として保存します。
* 元の形式をそのまま保存したい場合に使用します。

#### plainコーデック例 - Sourceノード

##### 条件

* 入力データ
  ```json
  {"name": "DataFlow","type": "pipeline","status": "running","level": "info"} 
  ```
* charset → `UTF-8`

##### 処理結果

```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### 伝達データ
```json
{
  "message": "{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"
}

```

### lineコーデック

* 各行を1つのメッセージとして処理します。
* Sourceノードでは入力された各行を`message`フィールドに文字列として保存し、Sinkノードではデータをformatに従ってテキスト行として出力します。
* 行単位の処理が必要な場合に使用します。
* lineコーデックの場合、delimiterを通じて出力されるメッセージの区切り文字を定義できます。 
    * デフォルト値は改行(`\n`)です。

#### lineコーデック例 - Sourceノード

##### 条件

* 入力データ
  ```text
  {"name":"DataFlow","type":"pipeline","status":"running","level":"info"}
  ```
* charset → `UTF-8`

##### 処理結果

```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### 伝達データ

```json
{
  "message": "{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"
}
```

#### lineコーデック例 - Sinkノード

##### 条件

* 入力メッセージ
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

##### 処理結果

```text
[info] DataFlow running
```

##### 出力データ

```text
[info] DataFlow running
```

### parquetコーデック

* Apache Parquet形式でデータを保存します。
* 多様な圧縮オプションに対応しており、大容量データの処理に適しています。
* 提供スキーマタイプ: `string`、`integer`、`long`、`float`、`double`、`boolean`、`timestamp`、`date`
* 提供圧縮形式: `SNAPPY(デフォルト値)`、`GZIP`、`LZ4_RAW`、`ZSTD`、`UNCOMPRESSED`
    * [圧縮形式の参照](https://parquet.apache.org/docs/file-format/data-pages/compression/)

#### parquetコーデックの例 - Sinkノード

##### 条件

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

##### 出力データ

Parquetはカラムベースのバイナリ形式で保存されるため、parquet-tools、Apache Spark、pandasなどの別途ツールを使用して確認できます。
