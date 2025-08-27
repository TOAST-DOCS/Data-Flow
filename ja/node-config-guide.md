## Data & Analytics > DataFlow > ノードタイプガイド

* ノードタイプは、手軽にフローを作成できるように事前に定義されたテンプレートです。
* ノードタイプの種類はSource、Filter、Branch、Sinkです。
* Source、Sinkノードタイプは、必ずテストを行ってエンドポイント情報が有効であることを確認することを推奨します。
* アクセス制御が設定されたデータソースに接続する場合は、DataFlow IP固定機能を使用する必要があります。
    * DataFlow IP固定機能を使用する場合は、サポートにお問い合わせください。

## Domain Specific Language(DSL)の定義

* フローの実行に必要なDSL定義です。

### Variable

* `{{ executionTime }}`
    * フロー実行時間
* 時間単位( unit )
    * 分 - `{{ MINUTE }}`
    * 時 - `{{ HOUR }}`
    * 日 - `{{ DAY }}`
    * 月 - `{{ MONTH }}`
    * 年 - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * 与えられた時間から`unit`で定義されたタイムゾーンの開始時間を返します。
    * [注意]韓国時間を基準に計算します。
    * ex\) \{\{ executionTime \| startOf: MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * 与えられた時間から`unit`で定義されたタイムゾーンの最後の時間を返します。
    * [注意]韓国時間を基準に計算します。
    * ex\) \{\{ executionTime \| endOf: MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * 与えられた時間から`unit`で定義されたタイムゾーンの`delta`だけ引いた時間を返します。
    * ex\) \{\{ executionTime \| subTime: 10, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 与えられた時間から`unit`で定義されたタイムゾーンの`delta`だけ足した時間を返します。
    * ex\) \{\{ executionTime \| addTime: 10, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10, MINUTE \}\}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * 与えられた時間を`formatStr`形式で返します。
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
    * ex\) \{\{ executionTime \| format: 'yyyy' \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| format: 'yyyy' \}\}
        * → 2022
* nested filter例
    * フローの実行が始まった日の03時のDSL表現
        * → \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## データ型別入力方法
### string
* 文字列を入力します。

### number
* 0以上の数字を入力します。
* 入力ウィンドウの右側の矢印を利用して値を1ずつ調整できます。

### boolean
* ドロップダウンメニューから`TRUE`または`FALSE`を選択します。

### enum
* ドロップダウンメニューから項目を選択します。

### array of strings
* 配列に入る文字列を一つずつ入力します。
* 文字列入力後、`+`ボタンをクリックすると、配列に文字列が挿入されます。
* ex) `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`を入力したい場合、`message`, `yyyy-MM-dd HH:mm:ssZ`, `ISO8601`の順に配列に文字列を挿入します。

### hash
* json形式の文字列を入力します。

## Source

* フローにデータを取り込むエンドポイントを定義するノードタイプです。

### 実行モード

* Sourceノードには実行モードが存在し、BATCHモードとSTREAMINGモーに分かれます。
    * STREAMINGモード：フローを終了せず、リアルタイムでデータを処理します。
    * BATCHモード：決められたデータを処理した後、フローを終了します。
* Sourceノードごとにサポートする実行モードが異なります。
* 1つのフローでは全てのSourceノードが同じ実行モードに設定されている必要があります。

## Sourceノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| タイプ | - | string | 各メッセージに与えられた値で`type`フィールドを作成します。 |  |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |
| タグ | - | array of strings | 各メッセージに与えられた値のタグを追加します。 |  |
| フィールド追加 | - | hash | カスタムフィールドを追加できます。<br/>`%{[depth1_field]}`で各フィールドの値を取得してフィールドを追加できます。 |  |

## フィールド追加の例

``` json
{
    "my_custom_field": "%{[json_body][logType]}"
}
```

## (NHN Cloud) Log & Crash Search

### ノードの説明

* (NHN Cloud) Log & Crash SearchノードはLog & Crash Searchからログを読み取るノードです。
* ノードにログ照会開始時間を設定できます。設しない場合は、フローを開始する時点からログを読み込みます。
* ノードに終了時間を入力しない場合は、ストリーミング形式でログを読み込みます。終了時間を入力すると終了時間までのログを読み込み、フローを終了します。
* ```現在、セッションログとクラッシュログはサポートしません。```
* Log & Crash Searchのログ検索APIのトークンに影響を受けます。
    * トークンが足りない場合はLog & Crash Searchにお問い合わせください。

### 実行モード
* STREAMING： `照会開始時間`以降のデータを継続して処理します。
* BATCH： `照会開始時間`、`照会終了時間`の間に該当するデータを全て処理し、フローを終了します。

### プロパティの説明

| プロパティ名      | デフォルト値 | データ型   | 説明                                                                                                                                                    | 備考 |
|-----------|-----|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey    | -   | string | Log & Crash Searchのアプリキーを入力します。                                                                                                                         |    |
| SecretKey | -   | string | Log & Crash Searchのシークレットキーを入力します。                                                                                                                       |    |
| 照会開始時間 | -   | string | ログ照会の開始時間を入力します。オフセットを含むISO 8601形式または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。 <br/>例：2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 照会終了時間 | -   | string | ログ照会の終了時間を入力します。オフセットを含むISO 8601形式または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。 <br/>例：2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 再試行回数   | -   | number | ログ照会が失敗した際に再試行する最大回数を入力します。                                                                                                                       |    |

* 再試行回数設定
    * 再試行回数だけ失敗した場合、それ以上ログ照会を試みず、フローは終了します。

### コーデック別メッセージ取り込み

* Log & Crash Searchは基本的に```JSON```形式のデータを扱います。
* コーデックを選択しない場合やplainの場合は、Log & Crash SearchログのJSON文字列を`message`というフィールドに含めます。
* Log & Crash Searchログの各フィールドを活用したい場合は、jsonコーデックを使用することを推奨します。

#### 未選択またはplain

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

### ノードの説明

* (NHN Cloud) CloudTrailはCloudTrailからデータを読み込むノードです。
* ノードにデータ照会開始時間を設定できます。設定しない場合はフローを開始する時点からデータを読み込みます。
* ノードに終了時間を入力しない場合は、ストリーミング形式でデータを読み込みます。終了時間を入力すると、終了時間までのデータを読み込み、フローを終了します。

### 実行モード
* STREAMING： `照会開始時間`以降のデータを継続して処理します。
* BATCH： `照会開始時間`、`照会終了時間`の間に該当するデータを全て処理し、フローを終了します。

### プロパティの説明

| プロパティ名     | デフォルト値 | データ型   | 説明                                                                                                                                                     | 備考 |
|----------|-----|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey   | -   | string | CloudTrailのアプリキーを入力します。                                                                                                                                  |    |
| 照会開始時間 | -   | string | データ照会の開始時間を入力します。オフセットを含む ISO 8601形式または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。 <br/>例：2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 照会終了時間 | -   | string | データ照会の終了時間を入力します。オフセットを含む ISO 8601形式または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。 <br/>例：2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 再試行回数  | -   | number | データ照会が失敗した際に再試行する最大回数を入力します。                                                                                                                       |    |

* 再試行回数設定
    * 再試行回数だけ失敗した場合と、それ以上データ照会を試みず、フローは終了します。

### コーデック別メッセージ取り込み

* CloudTrailは基本的に```JSON```形式のデータを扱っています。
* コーデックを選択しない場合、またはplainの場合は、CloudTrailデータのJSON文字列を`message`というフィールドに含めます。
* CloudTrailデータの各フィールドを活用したい場合は、jsonコーデックを使用することを推奨します。

#### 未選択またはplain

``` js
{
    "message":"{\\\"log\\\":\\\"CloudTrail\\\", \\\"Result\\\": \\\"Data\\\", \\\"@timestamp\\\": \\\"2023-12-06T08:09:24.887Z\\\", \\\"@version\\\": \\\"1\\\"}"
}
```

#### json

``` js
{"log":"CloudTrail", "Result": "Data"}
```


## Source > (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageからデータの入力を受け取るノードです。
* オブジェクト作成時間を基準に最も早く作成されたオブジェクトからデータを読み込みます。

### 実行モード
* STREAMING：`リスト更新周期`ごとにオブジェクトリストを更新し、新しく追加されたオブジェクトを読み込んでデータを処理します。
* BATCH：フロー開始時点でオブジェクトリストを一度取得し、オブジェクトを読み込んでデータを処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| バケット | - | string | データを読み込むバケット名を入力します。 |  |
| リージョン | - | string | リポジトリに設定されたリージョン情報を入力します。 |  |
| シークレットキー | - | string | S3が発行した認証情報シークレットキーを入力します。 |  |
| アクセスキー | - | string | S3が発行した認証情報アクセスキーを入力します。 |  |
| リスト更新周期 | - | number | バケットに含まれるオブジェクトリスト更新周期を入力します。 |  |
| メタデータを含めるかどうか | - | boolean | S3オブジェクトのメタデータをキーとして含めるかどうかを決定します。メタデータフィールドをSinkプラグインに公開するためには、filterノードタイプを組み合わせる必要があります(下のガイドを参照)。 | 作成されるフィールドは次のとおりです。<br/>last_modified:オブジェクトが最後に修正された時間<br/>content_length:オブジェクトサイズ<br/>key:オブジェクト名<br/>content_type:オブジェクト形式<br/>metadata:メタデータ<br/>etag: etag |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 |  |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 |  |
| 処理完了オブジェクトの削除 | false | boolean | プロパティ値がtrueの場合、読み込みが完了したオブジェクトを削除します。 |  |

### メタデータフィールドの使用方法

* `メタデータを含めるかどうか`設定を有効にすると、メタデータフィールドが作成されますが、別途一般フィールドに注入する作業を行わない限り、Sinkプラグインで表示されません。
* 設定有効化時(NHN Cloud) Object Storageプラグイン後のメッセージ例
```js
{
    // 一般フィールド
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "オブジェクト内容…"

    // メタデータフィールド
    // ユーザーが一般フィールドとして注入するまでSinkプラグインに表示できない。
    // "[@metadata][s3][last_modified]": 2024-01-05T01:35:50.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][metadata]": {}
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
}
```

* 本(NHN Cloud) Object Storage Sourceプラグインにフィールド追加オプションが存在しますが、データ入力と同時にフィールド追加作業を行うことができません。
* 任意のFilterプラグインの共通設定の中のフィールド追加オプションで一般フィールドとして追加します。
* フィールド追加オプションの例
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

* alter(フィールド追加オプション)プラグイン後のメッセージ例
```js
{
    // 一般フィールド
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "オブジェクト内容…"
    "last_modified": 2024-01-05T01:35:50.000Z
    "content_length": 220
    "key": "{filename}"
    "content_type": "text/plain"
    "metadata": {}
    "etag": "\"56ad65461e0abb907465bacf6e4f96cf\""

    // メタデータフィールド
    // "[@metadata][s3][last_modified]": 2024-01-05T01:35:50.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][metadata]": {}
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
}
```

### コーデック別のメッセージの取り込み

#### 未選択またはplain

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

### ノードの説明

* S3からデータの入力を受け取るノードです。
* オブジェクト作成時間を基準に最も早く作成されたオブジェクトからデータを読み込みます。

### 実行モード
* STREAMING：`リスト更新周期`ごとにオブジェクトリストを更新し、新しく追加されたオブジェクトを読み込んでデータを処理します。
* BAT

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考                                                                                                                                                                                                          |
| --- | --- | --- | --- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| エンドポイント | - | string | S3ストレージのエンドポイントを入力します。 | HTTP、HTTPS URL形式でのみ入力可能です。                                                                                                                                                                                |
| バケット | - | string | データを読み込むバケット名を入力します。 |                                                                                                                                                                                                              |
| リージョン | - | string | ストレージに設定されているリージョン情報を入力します。 |                                                                                                                                                                                                              |
| セッショントークン | - | string | AWSセッショントークンを入力します。 |                                                                                                                                                                                                              |
| シークレットキー | - | string | S3が発行した認証情報のシークレットキーを入力します。 |                                                                                                                                                                                                              |
| アクセスキー | - | string | S3が発行した認証情報のアクセスキーを入力します。 |                                                                                                                                                                                                              |
| リスト更新周期 | - | number | バケットに含まれるオブジェクトリストの更新周期を入力します。 |                                                                                                                                                                                                              |
| メタデータを含めるかどうか | - | boolean | S3オブジェクトのメタデータをキーとして含めるかどうかを決定します。メタデータフィールドをSinkプラグインに表示するにはfilterノードタイプを組み合わせる必要があります(下のガイドを参照)。 | 作成されるフィールドは次のとおりです。<br/>server_side_encryption:サーバー側の暗号化アルゴリズム<br/>last_modified:オブジェクトが最後に修正された時間<br/>content_length:オブジェクトサイズ<br/>key:オブジェクト名<br/>content_type:オブジェクト形式<br/>metadata:メタデータ<br/>etag: etag |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 |                                                                                                                                                                                                              |
| 除外キーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 |                                                                                                                                                                                                              |
| 処理完了オブジェクトの削除 | false | boolean | プロパティ値がtrueの場合、読み込み完了したオブジェクトを削除します。 |                                                                                                                                                                                                              |
| 追加設定 | - | hash | S3サーバーとの接続時に使用する追加設定を入力します。 | 利用可能な設定の全リストは、以下のリンクを参照してください。<br/>https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html<br/>例:<br/>{<br/>"force\_path\_style": true<br/>}                                                     |

### メタデータフィールドの使い方

* `メタデータを含めるかどうか`設定を有効にすると、メタデータフィールドが生成されますが、別途、一般フィールドに注入する作業を行わなければ、Sinkプラグインで公開しません。
* 設定有効化時、(Amazon) S3プラグイン後のメッセージ例
```js
{
    // 一般フィールド
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "オブジェクト内容…"

    // メタデータフィールド
    // ユーザーが一般フィールドとして注入するまではSinkプラグインに公開することができません。
    // "[@metadata][s3][server_side_encryption]": "AES256"
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][last_modified]": 2024-01-05T02:27:26.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][metadata]": {}
}
```

* 本(Amazon) S3 Sourceプラグインにフィールド追加オプションが存在しますが、データ入力と同時にフィールド追加作業ができません。
* 任意のFilterプラグインの共通設定中のフィールド追加オプションで一般フィールドとして追加します。
* フィールド追加オプション例
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

* alter(フィールド追加オプション)プラグイン後のメッセージ例
```js
{
    // 一般フィールド
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "オブジェクト内容…"
    "server_side_encryption": "AES256"
    "etag": "\"56ad65461e0abb907465bacf6e4f96cf\""
    "content_type": "text/plain"
    "key": "{filename}"
    "last_modified": 2024-01-05T01:35:50.000Z
    "content_length": 220
    "metadata": {}

    // メタデータフィールド
    // "[@metadata][s3][server_side_encryption]": "AES256"
    // "[@metadata][s3][etag]": "\"56ad65461e0abb907465bacf6e4f96cf\""
    // "[@metadata][s3][content_type]": "text/plain"
    // "[@metadata][s3][key]": "{filename}"
    // "[@metadata][s3][last_modified]": 2024-01-05T02:27:26.000Z
    // "[@metadata][s3][content_length]": 220
    // "[@metadata][s3][metadata]": {}
}
```

### コーデック別のメッセージの取り込み

#### 未選択またはplain

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

### ノードの説明

* Kafkaからデータを受信するノードです。

### 実行モード
* STREAMING：トピックに新しいメッセージが到着するたびにデータを処理します。

!!! danger "注意"
    * KafkaノードはBATCHモードをサポートしません。
    
### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考                                                                                                                                                                                                                                                                                                                                                      |
| --- | --- | --- | --- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ブローカーサーバーリスト | localhost:9092 | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はコンマ(`,`)で区切ります。 | [bootstrap.servers](https://kafka.apache.org/documentation/#consumerconfigs_bootstrap.servers)<br/>例：10.100.1.1:9092,10.100.1.2:9092                                                                                                                                                                                                                    |
| コンシューマーグループID | dataflow | string | Kafka Consumer Groupを識別するIDを入力します。 | [group.id](https://kafka.apache.org/documentation/#consumerconfigs_group.id)                                                                                                                                                                                                                                                                             |
| 内部トピックの除外有無 | true | boolean |  | [exclude.internal.topics](https://kafka.apache.org/documentation/#consumerconfigs_exclude.internal.topics)<br/>受信対象から`__consumer_offsets`のような内部トピックを除外します。                                                                                                                                                                                            |
| トピックパターン | - | string | メッセージを受信するKafkaトピックパターンを入力します。 | 例：`*-messages`                                                                                                                                                                                                                                                                                                                                          |
| クライアントID | dataflow | string | Kafka Consumerを識別するIDを入力します。 | [client.id](https://kafka.apache.org/documentation/#consumerconfigs_client.id)                                                                                                                                                                                                                                                                           |
| パーティション割り当てポリシー | - | string | Kafkaからメッセージを受信した時にコンシューマーグループにどのようにパーティションを割り当てるかを決定します。 | [partition.assignment.strategy](https://kafka.apache.org/documentation/#consumerconfigs_partition.assignment.strategy)<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| オフセット設定 | none | enum | コンシューマーグループのオフセットを設定する基準を入力します。 | [auto.offset.reset](https://kafka.apache.org/documentation/#consumerconfigs_auto.offset.reset)<br/>以下の設定はすべて、コンシューマーグループが既に存在する場合、既存のオフセットを維持します。<br/>none: コンシューマーグループが存在しない場合、エラーを返します。<br/>earliest: コンシューマーグループが存在しない場合、パーティションの最も古いオフセットに初期化します。<br/>latest: コンシューマーグループが存在しない場合、パーティションの最も新しいオフセットに初期化します。                                                                      |
| オフセットコミット周期 | 5000 | number | コンシューマーグループのオフセットを更新する周期を入力します。 | [auto.commit.internal.ms](https://kafka.apache.org/documentation/#consumerconfigs_auto.commit.interval.ms)                                                                                                                                                                                                                                               |
| オフセット自動コミット | true | boolean |  | [enable.auto.commit](https://kafka.apache.org/documentation/#consumerconfigs_enable.auto.commit)                                                                                                                                                                                                                                                         |
| キーのデシリアライズタイプ | org.apache.kafka.common.serialization.StringDeserializer | string | 受信するメッセージのキーをデシリアライズする方法を入力します。 | [key.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_key.deserializer)                                                                                                                                                                                                                                                             |
| メッセージデシリアライズタイプ | org.apache.kafka.common.serialization.StringDeserializer | string | 受信するメッセージの値をデシリアライズする方法を入力します。 | [value.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_value.deserializer)                                                                                                                                                                                                                                                         |
| メタデータ生成の有無 | false | boolean | プロパティ値がtrueの場合、メッセージに関するメタデータフィールドを生成します。メタデータフィールドをSinkプラグインに公開するには、filterノードタイプを組み合わせる必要があります(下記ガイド参照)。 | 生成されるフィールドは以下の通りです。<br/>topic: メッセージを受信したトピック<br/>consumer_group: メッセージの受信に使用したコンシューマーグループID<br/>partition: メッセージを受信したトピックのパーティション番号<br/>offset: メッセージを受信したパーティションのオフセット<br/>key: メッセージキーを含むByteBuffer                                                                                                                                                               |
| Fetch 最小 サイズ | - | number | 1回のfetchリクエストで取得するデータの最小サイズを入力します。 | [fetch.min.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.min.bytes)                                                                                                                                                                                                                                                               |
| 送信バッファサイズ | - | number | データの送信に使用するTCP sendバッファのサイズ(byte)を入力します。 | [send.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_send.buffer.bytes)                                                                                                                                                                                                                                                           |
| 再試行リクエスト周期 | 100 | number | 送信リクエストが失敗した際に再試行する周期(ms)を入力します。 | [retry.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_retry.backoff.ms)                                                                                                                                                                                                                                                             |
| 巡回冗長検査 | true | enum | メッセージのCRCを検査します。 | [check.crcs](https://kafka.apache.org/documentation/#consumerconfigs_check.crcs)                                                                                                                                                                                                                                                                         |
| サーバー再接続周期 | 50 | number | ブローカーサーバーへの接続が失敗した際に再試行する周期を入力します。 | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_reconnect.backoff.ms)                                                                                                                                                                                                                                                     |
| Pollタイムアウト | 100 | number | トピックから新しいメッセージを取得するリクエストのタイムアウト(ms)を入力します。 |                                                                                                                                                                                                                                                                                                                                                          |
| パーティションごとのFetch最大サイズ | - | number | パーティションごとに、1回のfetchリクエストで取得する最大のサイズを入力します。 | [max.partition.fetch.bytes](https://kafka.apache.org/documentation/#consumerconfigs_max.partition.fetch.bytes)                                                                                                                                                                                                                                           |
| サーバーリクエストタイムアウト | 30000 | number | 送信リクエストのタイムアウト(ms)を入力します。 | [request.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_request.timeout.ms)                                                                                                                                                                                                                                                         |
| TCP受信バッファサイズ | - | number | データの読み込みに使用するTCP receiveバッファのサイズ(byte)を入力します。 | [receive.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_receive.buffer.bytes)                                                                                                                                                                                                                                                     |
| session\_timeout\_ms | - | number | コンシューマーのセッションタイムアウト(ms)を入力します。<br/>コンシューマーがこの時間内にハートビートを送信できなかった場合、コンシューマーグループから除外されます。 | [session.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_session.timeout.ms)                                                                                                                                                                                                                                                         |
| 最大pollメッセージ数 | - | number | 1回のpollリクエストで取得する最大のメッセージ数を入力します。 | [max.poll.records](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.records)                                                                                                                                                                                                                                                             |
| 最大poll周期 | - | number | pollリクエスト間の最大周期(ms)を入力します。 | [max.poll.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.interval.ms)                                                                                                                                                                                                                                                     |
| Fetch最大サイズ | - | number | 1回のfetchリクエストで取得する最大のサイズを入力します。 | [fetch.max.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.bytes)                                                                                                                                                                                                                                                               |
| Fetch最大待機時間 | - | number | `Fetch 最小サイズ`で設定した量のデータが集まらない場合に、fetch リクエストを送信するまでの待機時間（ms）を入力します。 | [fetch.max.wait.ms](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.wait.ms)                                                                                                                                                                                                                                                           |
| コンシューマーヘルスチェック周期 | - | number | コンシューマーがheartbeatを送信する周期(ms)を入力します。 | [heartbeat.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_heartbeat.interval.ms)                                                                                                                                                                                                                                                   |
| メタデータ更新周期 | - | number | パーティションやブローカーサーバーの状態などを更新する周期(ms)を入力します。 | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms)                                                                                                                                                                                                                                                       |
| IDLEタイムアウト | - | number | データ送信がないコネクションを閉じるまでの待機時間（ms）を入力します。。 | [connections.max.idle.ms](https://kafka.apache.org/documentation/#consumerconfigs_connections.max.idle.ms)                                                                                                                                                                                                                                               |

### メタデータフィールドの使用方法

* `メタデータを作成するかどうか`設定を有効にすると、メタデータフィールドが生成されますが、別途一般フィールドに注入する作業を行わない限り、Sinkプラグインで公開しません。
* 設定を有効にした時のKafkaプラグイン以降のメッセージ例
    ```js
    {
        // 一般フィールド
        "@version": "1",
        "@timestamp": "2022-04-11T00:01:23Z"
        "message": "kafkaトピックメッセージ…"
        // メタデータフィールド
        // ユーザーが一般フィールドにインジェクションするまでSinkプラグインに公開できない
        // "[@metadata][kafka][topic]": "my-topic"
        // "[@metadata][kafka][consumer_group]": "my_consumer_group"
        // "[@metadata][kafka][partition]": "1"
        // "[@metadata][kafka][offset]": "123"
        // "[@metadata][kafka][key]": "my_key"
        // "[@metadata][kafka][timestamp]": "-1"
    }
    ```

* Kafka Sourceプラグインにフィールド追加オプションが存在しますが、データの取り込みと同時にフィールド追加作業を行えません。
* 任意のFilterプラグインの共通設定にあるフィールド追加オプションを利用して一般フィールドとして追加します。
    * フィールド追加オプションの例
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
    * alter(フィールド追加オプション)プラグイン以降のメッセージ例
```js
{
    // 一般フィールド
    "@version": "1",
    "@timestamp": "2022-04-11T00:01:23Z"
    "message": "kafkaトピックメッセージ…"
    "kafka_topic": "my-topic"
    "kafka_consumer_group": "my_consumer_group"
    "kafka_partition": "1"
    "kafka_offset": "123"
    "kafka_key": "my_key"
    "kafka_timestamp": "-1"
    // メタデータフィールド
    // "[@metadata][kafka][topic]": "my-topic"
    // "[@metadata][kafka][consumer_group]": "my_consumer_group"
    // "[@metadata][kafka][partition]": "1"
    // "[@metadata][kafka][offset]": "123"
    // "[@metadata][kafka][key]": "my_key"
    // "[@metadata][kafka][timestamp]": "-1"
}
```

### plainコーデック例

#### 入力メッセージ

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

#### 出力メッセージ

```js
{
    "message": "{\"hello\":\"world\",\"hey\":\"foo\"}"
}
```

### jsonコーデック例

#### 入力メッセージ

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

#### 出力メッセージ

```js
{
    "hello": "world!",
    "hey": "foo"
}
```

## Source > JDBC

### ノードの説明

* JDBCは与えられた周期でDBにクエリを実行して結果を取得するノードです。
* MySQL, MS-SQL, PostgreSQL, MariaDB, Oracleドライバーをサポートします。

### 実行モード
* STREAMING： `クエリ実行周期`ごとにクエリを実行し、データを処理します。
* BATCH：フロー開始時点でクエリを一度実行してデータを処理し、フローを終了します。

### プロパティ説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考                                                                                         |
| --- | --- | --- | --- |---------------------------------------------------------------------------------------------|
| ユーザー | - | string | DBユーザーを入力します。 |                                                                                             |
| 接続文字列 | - | string | DB接続情報を入力します。 | 例：`jdbc:mysql://my.sql.endpoint:3306/my_db_name`                                           |
| パスワード | - | string | ユーザーパスワードを入力します。 |                                                                                             |
| クエリ | - | string | メッセージを生成するためのクエリを作成します。 |                                                                                             |
| カラム名の小文字化 | true | boolean | クエリ結果として得られるカラム名を小文字にするかどうかを決定します。 |                                                                                             |
| クエリ実行周期 | `* * * * *` | string | クエリの実行周期をcronライクな表現で入力します。 |                                                                                             |
| トラッキングカラム | - | string | 追跡するカラムを選択します。 | 事前定義されたパラメータ`:sql_last_value`で、最後のクエリ結果で追跡するカラムに該当する値を使用できます。<br>以下のクエリ作成方法を参考にしてください。 |
| トラッキングカラム種類 | number | string | 追跡するカラムのデータ種類を選択します。 | 例：`numeric` or `timestamp`                                                                 |
| タイムゾーン | - | string | timestampタイプのカラムを人間が読める形式の文字列に変換する際に使用するタイムゾーンを定義します。 | 例：`Asia/Seoul`                                                                             |
| ページング適用の有無 | false | boolean | クエリにページングを適用するかどうかを決定します。 | ページングが適用されると、クエリは複数に分割して実行され、順序は保証されません。                                               |
| ページサイズ | - | number | ページングが適用されたクエリで、一度に照会するページサイズを決定します。 |                                                                                             |

### クエリの作成方法

* `:sql_last_value`を使って最後に実行されたクエリの結果で`トラッキングカラム`に該当する値を使うことができます(初期値は`トラッキングカラムの種類`が`numeric`なら`0`, `timestamp`なら`1970-01-01 00:00:00`)。

``` sql
SELECT * FROM MY_TABLE WHERE id > :sql_last_value
```

* 特定の条件を追加したい場合は、条件と一緒に`:sql_last_value`を追加します。

```sql
SELECT * FROM MY_TABLE WHERE id > :sql_last_value and id > custom_value order by id ASC
```

### コーデック別メッセージ入力

#### 未選択またはplain

``` js
{
    "id": 1,
    "name": "dataflow",
    "deleted": false
}
```

## Filter
* 取り込まれたデータをどのように処理するかを定義するノードタイプです。

## Filterノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |
| タグの追加 | - | array of strings | 各メッセージにタグを追加します。 |  |
| タグの削除 | - | array of strings | 各メッセージに与えられたタグを削除します。 |  |
| フィールドの削除 | - | array of strings | 各メッセージのフィールドを削除します。 |  |
| フィールド追加 | - | hash | カスタムフィールドを追加できます。<br/>`%{[depth1_field]}`で各フィールドの値を取得してフィールドを追加できます。 |  |

## Filter > Alter

### ノード説明

* メッセージフィールドの値を別の値に変更します。
* 最上位フィールドのみ変更できます。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| フィールドの変更 | - | array of strings | フィールドの値を与えられた値と比較して同じ場合、そのフィールドの値を指定された値に修正します。 |  |
| フィールドの上書き | - | array of strings | フィールド値を与えられた値と比較して同じ場合、他のフィールドの値を与えられた値に修正します。 |  |
| Coalesce | - | array of strings | 1つのフィールドに続くフィールドのうち、最初にnullではない値を割り当てます。 |  |

### 設定適用順序

* 各設定はプロパティ説明に記載された順番で適用されます。

#### 条件

* フィールド変更→ `["logType", "ERROR", "FAIL"]`
* フィールド上書き→ `["logType", "FAIL", "isBillingTarget", "false"]`

#### 入力メッセージ

```json
{
    "logType": "ERROR",
    "isBillingTarget": "true"
}
```

#### 出力メッセージ

```json
{
    "logType": "FAIL",
    "isBillingTarget": "false"
}
```

### フィールドの上書き例

#### 条件

* フィールドの上書き→ `["logType", "ERROR", "isBillingTarget", "false"]`

#### 入力メッセージ

```json
{
    "logType": "ERROR", 
    "isBillingTarget": "true"
}
```

#### 出力メッセージ

```json
{
    "logType": "ERROR",
    "isBillingTarget": "false"
}
```

### フィールドの変更例

#### 条件

* フィールドの変更→ `["reason", "CONNECTION_TIMEOUT", "MONGODB_CONNECTION_TIMEOUT"]`

#### 入力メッセージ

```js
{
    "reason": "CONNECTION_TIMEOUT"
}
```

#### 出力メッセージ

```js
{
    "reason": "MONGODB_CONNECTION_TIMEOUT"
}
```

### Coalesce例

#### 条件

* Coalesce → `["reason", "%{webClientReason}", "%{mongoReason}", "%{redisReason}"]`

#### 入力メッセージ

```js
{
    "mongoReason": "COLLECTION_NOT_FOUND"
}
```

#### 出力メッセージ

```js
{
    "reason": "COLLECTION_NOT_FOUND",
    "mongoReason": "COLLECTION_NOT_FOUND"
}
```

## Cipher

### ノードの説明

* メッセージフィールドの値を暗号化または復号するノードです。
* 暗号化キーはSecure Key Manager対称鍵を参照します。
    * Secure Key Managerの対称鍵は、Secure Key ManagerのWebコンソールまたはSecure Key Managerのキー追加APIを利用して作成できます。
    * 1つのフローに複数のCipherノードが含まれていても、すべてのCipherノードは必ず1つのSecure Key Managerキーリファレンスのみ参照できます。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| モード | - | enum | 暗号化モードと復号モードのいずれかを選択します。 | リストから1つを選択します。 |
| アプリケーションキー | - | string | 暗号化/復号に使用するキーを保存したSKMアプリケーションキーを入力します。 |  |
| キーID | - | string | 暗号化/復号に使用するキーを保存したSKMキーIDを入力します。 |  |
| キーバージョン | - | string | 暗号化/復号に使用するキーを保存したSKMキーバージョンを入力します。 |  |
| 暗号化/復号キーの長さ | 16 | number | 暗号化/復号キーの長さを入力します。 |  |
| IVランダム長 | - | number | Initial Vectorのrandom bytes長を入力します。 |  |
| ソースフィールド | - | string | 暗号化/復号するフィールド名を入力します。 |  |
| 保存するフィールド | - | string | 暗号化/復号結果を保存するフィールド名を入力します。 |  |

### encrypt例

#### 条件

* mode → `encrypt`
* アプリケーションキー → `SKMアプリケーションキー`
* キーID → `SKM対称鍵ID`
* キーバージョン → `1`
* IVランダム長 → `16`
* ソースフィールド → message
* 保存するフィールド → encrypted\_message

#### 入力メッセージ

``` js
{
    "message": "this is plain message"
}
```

#### 出力メッセージ

``` js
{
    "message": "this is plain message",
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

### decrypt例

#### 条件

* mode → `decrypt`
* アプリケーションキー → `SKMアプリケーションキー`
* キーID → `SKM対称鍵ID`
* キーバージョン → `1`
* IVランダム長 → `16`
* ソースフィールド → message
* 保存するフィールド → decrypted\_message

#### 入力メッセージ

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

#### 出力メッセージ

``` js
{
    "decrypted_message": "this is plain message",
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

## Filter > (Logstash) Grok

### ノードの説明

* 文字列を決められたルールに沿って解析して各設定フィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| Match | - | hash | 解析する文字列の情報を入力します。 |  |
| パターン定義 | - | hash | 解析するトークンのルールのユーザー定義パターンを正規表現で入力します。 | システム定義パターンについては以下のリンクをご確認ください。<br/>https://github.com/logstash-plugins/logstash-patterns-core/blob/main/patterns/legacy/grok-patterns |
| 失敗タグ | - | array of strings | 文字列の解析に失敗した場合に定義するタグ名を入力します。 |  |
| タイムアウト | 30000 | number | 文字列の解析が完了するまでの待機時間を入力します。 |  |
| 上書き | - | array of strings | 解析後に指定されたフィールドに値を書き込む時、そのフィールドにすでに値が定義されている場合は上書きするフィールド名を入力します。 |  |
| 名前が指定された値のみ保存 | true | boolean | プロパティ値がtrueの場合、名前が指定されていない解析結果を保存しません。 |  |
| 空の文字列をキャプチャ | false | boolean | プロパティ値がtrueの場合、空の文字列もフィールドに保存します。 |  |
| Match時に終了するかどうか | true | boolean | プロパティ値がtrueの場合、grok match結果がtrueならプラグインを終了します。 |  |

### Grok解析例

#### 条件

* Match → `{ "message": "%{IP:clientip} %{HYPHEN} %{USER} \[%{HTTPDATE:timestamp}\] \"%{WORD:verb} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}\" %{NUMBER:response} %{NUMBER:bytes}" }`
* パターン定義 → `{ "HYPHEN": "-*" }`

#### 入力メッセージ

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326"
}
```

#### 出力メッセージ

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

### ノードの説明

* CSV形式のメッセージを解析してフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 保存するフィールド | - | string | CSV解析結果を保存するフィールド名を入力します。 |  |
| Quote | " | string | カラムフィールドを分割する文字を入力します。 |  |
| 最初の行を無視するかどうか | false | boolean | プロパティ値がtrueの場合、読み込んだデータの最初の行に入力されたカラム名を無視します。 |  |
| カラム | - | array of strings | カラム名を入力します。 |  |
| セパレータ | , | string | カラムを区切る文字列を入力します。 |  |
| ソースフィールド | message | string | CSV解析するフィールド名を入力します。 |  |
| スキーマ | - | hash | 各カラムの名前とデータ型をdictionary形式で入力します。 | カラムに定義されたフィールドとは別に登録します。<br/>データ型は基本的にstringであり、他のデータ型に変換が必要な場合はスキーマ設定を活用します。<br/>可能なデータ型は次のとおりです。<br/>integer、float、date、date_time、boolean |

### データ型がないCSV解析の例

#### 条件

* ソースフィールド → `message`
* カラム → `["one", "two", "t hree"]`

#### 入力メッセージ

```js
{
    "message": "hey,foo,\\\"bar baz\\\""
}
```

#### 出力メッセージ

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```

### データ型がないCSV解析の例

#### 条件

* ソースフィールド → `message`
* カラム → `["one", "two", "t hree"]`

#### 入力メッセージ

```js
{
    "message": "hey,foo,\\\"bar baz\\\""
}
```

#### 出力メッセージ

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```

### データ型変換が必要なCSV解析の例

#### 条件

* ソースフィールド → `message`
* カラム → `["one", "two", "t hree"]`
* スキーマ → `{"two": "integer", "t hree": "boolean"}`

#### 入力メッセージ

```js
{
    "message": "\\\"wow hello world!\\\", 2, false"
}
```

#### 出力メッセージ

```js
{
    "message": "\\\"wow hello world!\\\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### ノードの説明

* JSON文字列を解析して指定されたフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド | message | string | JSON文字列を解析するフィールド名を入力します。 |  |
| 保存するフィールド | - | string | JSON解析結果を保存するフィールド名を入力します。<br/>プロパティ値を指定していない場合はrootフィールドに結果を保存します。 |  |

### JSON解析の例

#### 条件

* ソースフィールド → `message`
* 保存するフィールド → `json_parsed_messsage`

#### 入力メッセージ

```js
{
    "message": "{\\\"json\\\": \\\"parse\\\", \\\"example\\\": \\\"string\\\"}"
}
```

#### 出力メッセージ

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

### ノードの説明

* 文字列を決められたルールに沿って解析して、各設定されたフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| Match | - | json | 解析する文字列の情報を入力します。 |  |
| パターン定義 | - | json | 解析するトークンのルールのユーザー定義パターンを正規表現で入力します。 | システム定義パターンは以下のリンクをご確認ください。<br/>http://grokdebug.herokuapp.com/patterns |
| 失敗タグ | - | array of strings | 文字列の解析に失敗する場合、定義するタグ名を入力します。 |  |
| タイムアウト | 30000 | number | 文字列の解析が行われるまでの待機時間を入力します。 |  |
| 上書き | - | array of strings | 解析後、指定されたフィールドに値を書き込む際、そのフィールドにすでに値が定義されている場合、上書きするフィールド名を入力します。 |  |
| 名前が指定された値のみ保存 | - | boolean | 名前が指定されていない解析結果を保存するかどうかを選択します。 |  |
| 空の文字列をキャプチャ | - | boolean | 空の文字列もフィールドに保存するかどうかを選択します。 |  |

### Grok解析例

#### 条件

* Match → `{ "message": "%{IP:clientip} %{HYPHEN} %{USER} \[%{HTTPDATE:timestamp}\] \"%{WORD:verb} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}\" %{NUMBER:response} %{NUMBER:bytes}" }`
* パターン定義 → `{ "HYPHEN": "-*" }`

#### 入力メッセージ

```js
{
    "message": "127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] \\\"GET /apache_pb.gif HTTP/1.0\\\" 200 2326"
}
```

#### 出力メッセージ

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

### ノードの説明

* Date文字列を解析してtimestamp形式で保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド | - | string | 文字列を取得するためのフィールド名を入力します。 |  |
| フォーマット | - | array of strings | 文字列を取得するための形式を入力します。 | 事前定義された形式は次のとおりです。<br/>ISO8601, UNIX, UNIX_MS, TAI64N |
| Locale | - | Date文字列を分析するために使用するLocaleを入力します。 | ex) en, en-US, ko-kr |
| 保存するフィールド | - | string | Date文字列解析結果を保存するフィールド名を入力します。 |  |
| 失敗タグ | - | array of strings | Date文字列の解析に失敗した場合に定義するタグ名を入力します。 |  |
| タイムゾーン | - | string | 日付のタイムゾーンを入力します。 |  |

### Date文字列の解析例

#### 条件

* Match → `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* 保存するフィールド → `time`
* タイムゾーン → `Asia/Seoul`

#### 入力メッセージ

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### 出力メッセージ

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### ノードの説明

* UUIDを作成してフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| UUID保存フィールド | - | string | UUID作成結果値を保存するフィールド名を入力します。 |  |
| 上書き | - | boolean | 指定されたフィールド名に値が存在する場合、これを上書きするかどうかを選択します。 |  |

### UUID作成例

#### 条件

* UUID保存フィールド → `userId`

#### 入力メッセージ

```js
{
    "message": "uuid test message"
}
```

#### 出力メッセージ

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Split

### ノードの説明

* 1つのメッセージを複数のメッセージに分割するノードです。
* 設定に従って解析した結果を基にメッセージを分割します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド | - | string | メッセージを分離するフィールド名を入力します。 |  |
| 保存するフィールド | - | string | 分離されたメッセージを保存するフィールド名を入力します。 |  |
| セパレータ | `\n` | string |  |  |

### 基本メッセージ分割例

#### 条件

* ソースフィールド → `message`

#### 入力メッセージ

```js
{
    "message": [
        {"number": 1},
        {"number": 2}
    ]
}
```

#### 出力メッセージ

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

### 文字列解析後の分割例

#### 条件

* ソースフィールド → `message`
* セパレータ → `,`

#### 入力メッセージ

```js
{
    "message": "1,2"
}
```

#### 出力メッセージ

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

### 文字列解析後、他のフィールドに分割する例

#### 条件

* ソースフィールド → `message`
* 保存するフィールド → `target`
* セパレータ → `,`

#### 入力メッセージ

```js
{
    "message": "1,2"
}
```

#### 出力メッセージ

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

### ノードの説明

* JSON文字列を解析して指定されたフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| Byte数 | - | number | 文字列を表現する最大byte数を入力します。 |  |
| ソースフィールド | - | string | truncate対象フィールド名を入力します。 |  |

### JSON解析例

#### 条件

* Byte数 → 10
* ソースフィールド → `message`

#### 入力メッセージ

```js
{
    "message": "このメッセージは長すぎます。"
}
```

#### 出力メッセージ

```js
{
    "message": "このメ"
}
```
## Filter > Mutate

### ノード説明

* フィールドの値を変形するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| デフォルト値設定 | - | hash | null値をデフォルト値に置き換えます。 |  |
| フィールド名変更 | - | hash | フィールド名を変更します。 |  |
| フィールド値更新 | - | hash | フィールドの値を新しい値に置き換えます。フィールドが存在しない場合は、何も動作しません。 |  |
| 値置換 | - | hash | フィールドの値を新しい値に置き換えます。フィールドがない場合は新規に作成します。  |  |
| 型変換 | - | hash | フィールドの値を他の型に変換します。 | サポートするタイプはinteger, interger_eu, float, float_eu, string, booleanです。 |
| 文字列置換 | - | array | 正規表現で文字列の一部を置換します。 |  |
| 大文字変換 | - | array | フィールドの文字列を大文字に変更します。 |  |
| 最初の文字を大文字化 | - | array | フィールドの最初の文字を大文字に変換し、残りの文字は小文字に変換します。 |  |
| 小文字変換 | - | array | 対象フィールドの文字列を小文字に変更します。 |  |
| 空白除去 | - | array | フィールドの文字列の前後の空白を除去します。 |  |
| 文字列分割 | - | hash | 区切り文字を利用して文字列を配列に分割します。 |  |
| 配列の結合 | - | hash | 区切り文字を利用して配列の要素を1つの文字列に結合します。 |  |
| フィールドの結合 | - | hash | 2つのフィールドを結合します。 |  |
| フィールドコピー | - | hash | 既存のフィールドを別のフィールドにコピーします。フィールドが存在する場合は上書きします。 |  |
| 失敗タグ | _mutate_error | string | エラーが発生した場合、定義するタグを入力します。 |  |

### 設定適用順序
* 各設定はプロパティ説明に記載された順番で適用されます。

#### 条件

* フィールド値更新→ `{"fieldname": "new value"}`
* 大文字変換→ `["fieldname"]`

#### 入力メッセージ

```json
{
    "fieldname": "old value"
}
```

#### 出力メッセージ

```json
{
    "fieldname": "NEW VALUE"
}
```

### デフォルト値設定例

#### 条件

```json
{
  "fieldname": "default_value"
}
```

#### 入力メッセージ

```json
{
  "fieldname": null
}
```

#### 出力メッセージ

```json
{
  "fieldname": "default_value"
}
```

### フィールド名変更例

#### 条件
```json
{
  "fieldname": "changed_fieldname"
}
```

#### 入力メッセージ

```json
{
  "fieldname": "Hello World!"
}
```

#### 出力メッセージ

```json
{
  "changed_fieldname": "Hello World!"
}
```

### フィールド値更新例

#### 条件

```json
{
  "fieldname": "%{other_fieldname}: %{fieldname}",
  "not_exist_fieldname": "DataFlow"
}
```

#### 入力メッセージ

```json
{
  "fieldname": "Hello World!",
  "other_fieldname": "DataFlow"
}
```

#### 出力メッセージ

```json
{
  "fieldname": "DataFlow: Hello World!",
  "other_fieldname": "DataFlow"
}
```

### 値代替例

#### 条件

```json
{
  "fieldname": "%{other_fieldname}: %{fieldname}",
  "not_exist_fieldname": "DataFlow"
}
```

#### 入力メッセージ

```json
{
  "fieldname": "Hello World!",
  "other_fieldname": "DataFlow"
}
```

#### 出力メッセージ

```json
{
  "fieldname": "DataFlow: Hello World!",
  "other_fieldname": "DataFlow",
  "not_exist_fieldname": "DataFlow"
}
```

### 型変換例

#### 条件

```
{
  "message1": "integer",
  "message2": "boolean"
}
```

#### 入力メッセージ

```json
{
  "message1": "1000",
  "message2": "true"
}
```

#### 出力メッセージ

```json
{
    "message1": 1000,
    "message2": true
}
```

#### タイプ説明

* integer
  * 文字列を整数型に変換します。コンマで区切られた文字列をサポートします。小数点以下のデータは削除されます。
    * 例："1,000.5" -> `1000`
  * 実数型データを整数型に変換します。小数点以下のデータは削除されます。
  * boolean型のデータを整数型に変換します。true`は`1`, `false`は`0`に変換されます。
* integer_eu
  * データを整数型に変換します。ドットで区切られた文字列をサポートします。小数点以下のデータは削除されます。
    * 例："1.000,5" -> `1000`
  * 実数型とboolean型データはintegerと同じです。
* float
  * 整数型データを実数型に変換します。
  * 文字列を実数型に変換します。コンマで区切られた文字列をサポートします。
    * 例："1,000.5" -> `1000.5`
  * boolean型のデータを整数型に変換します。true`は`1.0`, `false`は`0.0`に変換されます。
* float_eu
  * データを実数型に変換します。ドットで区切られた文字列をサポートします。
    * 例："1.000,5" -> `1000.5`
  * 実数型とboolean型データはfloatと同じです。
* string
  * データをUTF-8エンコードの文字列に変換します。
* boolean
  * 整数型データをboolean型に変換します。1`は`true`, `0`は`false`に変換されます。
  * 実数型データをboolean型に変換します。1.0`は`true`, `0.0`は`false`に変換されます。
  * 文字列をboolean型に変換します。 `"true"`, `"t"`, `"yes"`, `"y"`, `"1"`, `"1.0"`は`true`, `"false"`, `"f"`, `"no"`, `"n"`, `"0"`, `"0.0"`は`false`に変換されます。空の文字列は`false`に変換されます。
* 配列データは、各要素が上記の説明に従って変換されます。

### 文字列置換の例

#### 条件

```json
["fieldname", "/", "_", "fieldname2", "[\\?#-]", "."]
```
* `fieldname`フィールドの文字列値の`/`を`_`に、`fieldname2`フィールドの文字列値の`\`, `?`, `#`, `-`を`.`に置換します。

#### 入力メッセージ
```json
{
  "fieldname": "Hello/World",
  "fieldname2": "Hello\\?World#Test-123"
}
```

#### 出力メッセージ
```json
{
  "fieldname": "Hello_World",
  "fieldname2": "Hello.World.Test.123"
}
```

### 大文字変換例

#### 条件

```json
["fieldname"]
```

#### 入力メッセージ

```json
{
  "fieldname": "hello world"
}
```

#### 出力メッセージ

```json
{
  "fieldname": "HELLO WORLD"
}
```

### 最初の文字を大文字化する例

#### 条件

```json
["fieldname"]
```

#### 入力メッセージ

```json
{
  "fieldname": "hello world"
}
```

#### 出力メッセージ

```json
{
  "fieldname": "Hello world"
}
```

### 小文字変換例
#### 条件

```json
["fieldname"]
```

#### 入力メッセージ

```json
{
  "fieldname": "HELLO WORLD"
}
```

#### 出力メッセージ

```json
{
  "fieldname": "hello world"
}
```

### 空白を削除する例

#### 条件

```json
["field1", "field2"]
```

#### 入力メッセージ

```json
{
  "field1": "Hello World!   ",
  "field2": "   Hello DataFlow!"
}
```

#### 出力メッセージ

```json
{
  "field1": "Hello World!",
  "field2": "Hello DataFlow!"
}
```

### 文字列分割例

#### 条件

```json
{
  "fieldname": ","
}
```

#### 入力メッセージ

```json
{
  "fieldname": "Hello,World"
}
```

#### 出力メッセージ

```json
{
  "fieldname": ["Hello", "World"]
}
```

### 配列結合例

#### 条件

```json
{
  "fieldname": ","
}
```

#### 入力データ

```json
{
  "fieldname": ["Hello", "World"]
}
```

#### 出力メッセージ

```json
{
  "fieldname": "Hello,World"
}
```

### フィールド結合例
#### 条件

```json
{
  "array_data1": "string_data1",
  "string_data2": "string_data1",
  "json_data1": "json_data2"
}
```

#### 入力メッセージ

```json
{
  "array_data1": ["array_data1"],
  "string_data1": "string_data1",
  "string_data2": "string_data2",
  "json_data1": {"json_field1": "json_data1"},
  "json_data2": {"json_field2": "json_data2"}
}
```

#### 出力メッセージ

```json
{
  "array_data1": ["array_data1", "string_data1"],
  "string_data1": "string_data1",
  "string_data2": ["string_data2", "string_data1"],
  "json_data1": {"json_field2" : "json_data2", "json_field1": "json_data1"},
  "json_data2": {"json_field2": "json_data2"}
}
```
* array + array = 2つのarrayを結合
* array + string = arrayにstringを追加
* string + string = 2つのstringを要素とするarrayに変換
* json + json = json結合

### フィールドコピー例

#### 条件

```json
{
  "source_field": "dest_field"
}
```

#### 入力メッセージ

```json
{
  "source_field": "Hello World!"
}
```

#### 出力メッセージ

```json
{
  "source_field": "Hello World!",
  "dest_field": "Hello World!"
}
```

## Sink

* Filter作業が完了したデータをロードするエンドポイントを定義するノードタイプです。

## Sinkノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |

## (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageにデータをアップロードするノードです。
* OBSに作成されるオブジェクトは、基本的に次のパスフォーマットに合わせて出力されます。
    * `/{container_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| リージョン | - | enum | Object Storage商品のリージョンを入力します。 |  |
| バケット | - | string | バケット名を入力します。 |  |
| シークレットキー | - | string | S3 API認証情報のシークレットキーを入力します。 |  |
| アクセスキー | - | string | S3 API認証情報のアクセスキーを入力します。 |  |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | オブジェクトをアップロードする時に名前の前につけるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | @timestamp | string | Prefixに適用する時間フィールドを入力します。 |  |
| Prefix時間フィールドタイプ | DATE_FILTER_RESULT | enum | Prefixに適用する時間フィールドのタイプを入力します。 |  |
| Prefixタイムゾーン | UTC | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 |  |
| Prefix時間適用fallback  | _prefix_datetime_parse_failure | string | Prefix時間適用に失敗した場合に代替するPrefixを入力します。 |  |
| エンコード | none | enum | エンコードするかどうかを入力します。 gzipエンコードを使用できます。 |  |
| オブジェクトローテーションポリシー | size\_and\_time | enum | オブジェクトの作成ルールを決定します。 | size\_and\_time:オブジェクトのサイズと時間を利用して決定<br/>size:オブジェクトのサイズを利用して決定<br/>time:時間を利用して決定 |
| 基準時刻 | 15 | number | オブジェクトを分割する基準となる時間を設定します。 | オブジェクトローテーションポリシーがsize\_and\_timeまたはtimeの場合に設定 |
| 基準オブジェクトサイズ | 5242880 | number | オブジェクトを分割する基準となるサイズを設定します。 | オブジェクトローテーションポリシーがsize\_and\_timeまたはsizeの場合に設定 |

### jsonコーデックの出力例

#### 条件

* リージョン → `KR1`
* バケット → `obs-test-container`
* アクセスキー → `******`
* シークレットキー → `******`

#### 入力メッセージ

``` json
{"hidden":"Hello DataFlow!","message":"Hello World", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### 出力値

* パス

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* 出力メッセージ

``` json
{"@timestamp":"2022-11-21T07:49:20.000Z","host":"755b65d82bd0","hidden":"Hello DataFlow!","@version":"1","sequence":0,"message":"Hello World"}
```

### plainコーデック出力例 - messageフィールド存在する場合

#### 条件

* リージョン → `KR1`
* バケット → `obs-test-container`
* アクセスキー → `******`
* シークレットキー → `******`

#### 入力メッセージ

``` json
{
    "message": "Hello World!",
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 出力値

* パス

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* 出力メッセージ

```
2022-11-21T07:49:20.000Z e0e40e03dd94 Hello World
```

### plainコーデック出力例 - messageフィールドが存在しない場合

#### 条件

* リージョン → `KR1`
* バケット → `obs-test-container`
* アクセスキー → `******`
* シークレットキー → `******`

#### 入力メッセージ

``` json
{
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

### Parquetコーデックプロパティ説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| parquet圧縮コーデック | SNAPPY | enum | parquetファイル変換時に使用する圧縮コーデックを入力します。 | [参照](https://parquet.apache.org/docs/file-format/data-pages/compression/) |

#### 出力値

* パス

```
/obs-test-container/year=2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* 出力メッセージ

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
```

### Prefix例 - フィールド

#### 条件

* バケット→ `obs-test-container`
* Prefix → `/dataflow/%{deployment}`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス

```
/obs-test-container/dataflow/production/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

### Prefix例 - 時間

#### 条件

* バケット→ `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド→ `logTime`
* Prefix時間フィールドタイプ→ `ISO8601`
* Prefixタイムゾーン→ `Asia/Seoul`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス

```
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

### Prefix例 - 時間適用が失敗した場合

#### 条件

* バケット→ `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド→ `logTime`
* Prefix時間フィールドタイプ→ `TIMESTAMP_SEC`
* Prefixタイムゾーン→ `Asia/Seoul`
* Prefix時間適用fallback → `_failure`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス

```
/obs-test-container/_failure/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

## (Amazon) S3

### ノードの説明

* Amazon S3にデータをアップロードするノードです。

### プロパティの説明
| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| リージョン | - | enum | S3商品のリージョンを入力します。 | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| バケット | - | string | バケット名を入力します。 |  |
| アクセスキー | - | string | S3 API認証情報のアクセスキーを入力します。 |  |
| シークレットキー | - | string | S3 API認証情報のシークレットキーを入力します。 |  |
| 署名バージョン | - | enum | AWSリクエストを署名する時に使用するバージョンを入力します。 |  |
| セッショントークン | - | string | AWS一時認証情報のためのセッショントークンを入力します。 | [セッショントークンガイド](https://docs.aws.amazon.com/ja_kr/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) |
| Prefix | - | string | オブジェクトをアップロードする時に名前の前につけるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | @timestamp | string | Prefixに適用する時間フィールドを入力します。 |  |
| Prefix時間フィールドタイプ | DATE_FILTER_RESULT | enum | Prefixに適用する時間フィールドのタイプを入力します。 |  |
| Prefixタイムゾーン | UTC | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 |  |
| Prefix時間適用fallback  | _prefix_datetime_parse_failure | string | Prefix時間適用に失敗した場合に代替するPrefixを入力します。 |  |
| ストレージクラス | STANDARD | enum | オブジェクトをアップロードする時に使用するストレージクラスを設定します。 | [ストレージクラスガイド](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| エンコード | none | enum | エンコードするかどうかを入力します。gzipエンコードを使用できます。 |  |
| オブジェクトローテーションポリシー | size\_and\_time | enum | オブジェクトの作成ルールを決定します。 | size\_and\_time:オブジェクトのサイズと時間を利用して決定<br/>size:オブジェクトのサイズを利用して決定<br/>time:時間を利用して決定 |
| 基準時刻 | 15 | number | オブジェクトを分割する基準となる時間を設定します。 | オブジェクトローテーションポリシーがsize\_and\_timeまたはtimeの場合に設定 |
| 基準オブジェクトサイズ | 5242880 | number | オブジェクトを分割する基準となるサイズを設定します。 | オブジェクトローテーションポリシーがsize\_and\_timeまたはsizeの場合に設定 |
| ACL | private | enum | オブジェクトをアップロードする時に設定するACLポリシーを入力します。 |  |
| 追加設定 | { } | hash | S3に接続するための追加設定を入力します。 | [ガイド](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html) |

### 出力例

* OBSと同じです。

### Parquetコーデックプロパティ説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| parquet圧縮コーデック | SNAPPY | enum | parquetファイル変換時に使用する圧縮コーデックを入力します。 | [参照](https://parquet.apache.org/docs/file-format/data-pages/compression/) |

### 追加設定の例

#### follow redirects

* `true`に設定した場合、AWS S3で307レスポンスを返す場合はredirectパスを追跡

``` js
{
    follow_redirects → true
}
```

#### retry limit

* 4xx、5xxレスポンスの最大再試行回数

``` js
{
    retry_limit → 5
}
```

#### force\_path\_style

* `true`の場合、URLがvirtual-hosted-styleではなくpath-styleでなければなりません。[参照](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/VirtualHosting.html)

``` js
{
    force_path_style → true
}
```

## Sink > (Apache) Kafka

### ノードの説明

* Kafkaにデータを転送するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| トピック | - | string | メッセージを転送するKafkaトピック名を入力します。 |  |
| ブローカーサーバーリスト | localhost:9092 | string | Kafkaブローカーサーバーを入力します。サーバーが複数の場合はコンマ(`,`)で区切ります。 | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>例) 10.100.1.1:9092,10.100.1.2:9092 |
| クライアントID | dataflow | string | Kafka Producerを識別するIDを入力します。 | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| メッセージシリアライズタイプ | org.apache.kafka.common.serialization.StringSerializer | string | 転送するメッセージの値をシリアライズする方法を入力します。 | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| 圧縮タイプ | none | enum | 送信するデータを圧縮する方法を入力します。 | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br/>no| プロパティ名          | デフォルト値                                                   | データ型   | 説明                                                | 備考                                                                                                                                                                                                                                              |
|---------------|--------------------------------------------------------|--------|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| トピック           | -                                                      | string | メッセージを送信するKafkaトピック名を入力します。                       |                                                                                                                                                                                                                                                  |
| ブローカーサーバーリスト    | localhost:9092                                         | string | Kafkaブローカーサーバーを入力します。サーバーが複数の場合はコンマ(`,`)で区切ります。  | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>例：10.100.1.1:9092,10.100.1.2:9092                                                                                                            |
| クライアントID     | dataflow                                               | string | Kafka Producerを識別するIDを入力します。                    | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id)                                                                                                                                                                   |
| メッセージシリアライズタイプ   | org.apache.kafka.common.serialization.StringSerializer | string | 送信するメッセージの値をシリアライズする方法を入力します。                       | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer)                                                                                                                                                     |
| 圧縮タイプ        | none                                                   | enum   | 送信するデータを圧縮する方法を入力します。                           | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br/>none, gzip, snappy, lz4から選択                                                                                                                      |
| キーシリアライズタイプ     | org.apache.kafka.common.serialization.StringSerializer | string | 送信するメッセージのキーをシリアライズする方法を入力します。                       | [key.serializer](https://kafka.apache.org/documentation/#producerconfigs_key.serializer)                                                                                                                                                         |
| メタデータ更新周期  | 300000                                                 | number | パーティション、ブローカーサーバー状態などを更新する周期(ms)を入力します。               | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms)                                                                                                                                               |
| 最大リクエストサイズ     | 1048576                                                | number | 送信リクエストごとの最大サイズ(byte)を入力します。                         | [max.request.size](https://kafka.apache.org/documentation/#producerconfigs_max.request.size)                                                                                                                                                     |
| サーバー再接続周期    | 50                                                     | number | ブローカーサーバーに接続が失敗した時に再試行する周期を入力します。                 | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms)                                                                                                                                             |
| バッチサイズ        | 16384                                                  | number | バッチリクエストで送信するサイズ(byte)を入力します。                       | [batch.size](https://kafka.apache.org/documentation/#producerconfigs_batch.size)                                                                                                                                                                 |
| バッファメモリ       | 33554432                                               | number | Kafka送信に使用するバッファのサイズ(byte)を入力します。                | [buffer.memory](https://kafka.apache.org/documentation/#producerconfigs_buffer.memory)                                                                                                                                                           |
| 受信バッファサイズ     | 32768                                                  | number | データの読み込みに使用するTCP receiveバッファのサイズ(byte)を入力します。    | [receive.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_receive.buffer.bytes)                                                                                                                                             |
| 送信遅延時間     | 0                                                      | number | メッセージ送信を遅らせる時間を入力します。遅延したメッセージはバッチリクエストで一度に送信します。 | [linger.ms](https://kafka.apache.org/documentation/#producerconfigs_linger.ms)                                                                                                                                                                   |
| サーバーリクエストタイムアウト   | 40000                                                  | number | 送信リクエストのタイムアウト(ms)を入力します。                         | [request.timeout.ms](https://kafka.apache.org/documentation/#producerconfigs_request.timeout.ms)                                                                                                                                                 |
| メタデータ照会タイムアウト |                                                        | number |                                                    | [https://kafka.apache.org/documentation/#upgrade\_1100\_notable](https://kafka.apache.org/documentation/#upgrade_1100_notable)                                                                                                                   |
| 送信バッファサイズ     | 131072                                                 | number | データを送信するのに使用するTCP sendバッファのサイズ(byte)を入力します。     | [send.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_send.buffer.bytes)                                                                                                                                                   |
| ackプロパティ       | 1                                                      | enum   | ブローカーサーバーでメッセージを受信したことを確認する設定を入力します。 | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br/>0 - メッセージを受信したかどうかを確認しません。<br/>1 - トピックのleaderが、followerがデータをコピーすることを待たずにメッセージを受信したというレスポンスを返します。<br/>all - トピックのleaderが、followerがデータをコピーするのを待ってからメッセージを受信したというレスポンスを返します。 |
| リクエストの再接続周期    | 100                                                    | number | 送信リクエストが失敗した時に再試行する周期(ms)を入力します。                  | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms)                                                                                                                                                     |
| 再試行回数       | -                                                      | number | 送信リクエストが失敗した時に再試行する最大回数を入力します。                   | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br/>設定値を超えて再試行すると、データが消失する可能性があります。                                                                                                                            |

### jsonコーデックの出力例

#### 入力メッセージ

``` json
{
    "message": "Hello World!",
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 出力メッセージ

``` json
{"host":"0bc501d89f8c","message":"Hello World","hidden":"Hello DataFlow!","sequence":0,"@timestamp":"2022-11-21T07:49:20.000Z","@version":"1"}
```

### plainコーデック出力例

#### 入力メッセージ

``` json
{
    "message": "Hello World!",
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 出力メッセージ

```
2022-11-21T07:49:20.000Z 2898d492114d Hello World
```

### plainコーデック出力例 - messageフィールドが存在しない場合

#### 入力メッセージ

``` json
{
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 出力メッセージ

```
2022-11-21T07:49:20.000Z e5ef7ece9bb0 %{message}
```

## Sink > Stdout

### ノード説明

* 標準出力でメッセージを出力するノードです。
* Source、Filterノードで処理されたデータを確認する際に便利です。

### コーデック別出力例

#### 入力メッセージ

``` json
{
    "host": "data-flow-01",
    "message": "Hello World!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### plainコーデック出力メッセージ

```
2022-11-21T07:49:20Z data-flow-01 Hello World!
```

#### jsonコーデック出力例

```
{"host": "data-flow-01", "message": "Hello World!", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### lineコーデック出力例

* formatは次のように設定
  * `%{message} %{host}`

```
Hello World! data-flow-01
```

#### debugコーデック出力例

```
{
        "host" => "data-flow-01",
     "message" => "Hello World!",
  "@timestamp" => 2022-11-21T07:49:20Z
}
```

## Branch

* 取り込まれたデータの値に基づいてフロー分岐を定義するノードタイプです。

## IF

### ノードの説明

* 条件文でメッセージをフィルタリングするノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 条件文 | - | string | メッセージをフィルタリングする条件を入力します。 |  |

### フィルタリング例 - first depth field reference

#### 条件

* 条件文 → `[logLevel] == "ERROR"`

#### 通過メッセージ

``` json
{
       "logLevel": "ERROR"
}
```

#### 漏れメッセージ

``` json
{
   "logLevel": "INFO"
}
```

### フィルタリング例 - second depth field reference

#### 条件

* 条件文 → `[response][status] == 200`

#### 通過メッセージ

``` json
{
    "resposne": {
        "status": 200
    }
}
```

#### 漏れメッセージ

``` json
{
    "resposne": {
        "status": 404
    }
}
```
