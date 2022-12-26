## ノードタイプ

* ノードタイプは、手軽にフローを作成できるように先定義されたテンプレートです。
* ノードタイプの種類はSource、Filter、Branch、Sinkです。
* Source、Sinkノードタイプは、必ずテストを行ってエンドポイント情報が有効であることを確認することを推奨します。

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
    * 与えられた時間から`unit`で定義された時間帯の開始時間を返します。
    * [注意]韓国時間を基準に計算します。
    * ex\) \{\{ executionTime \| startOf: MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * 与えられた時間から`unit`で定義された時間帯の最後の時間を返します。
    * [注意]韓国時間を基準に計算します。
    * ex\) \{\{ executionTime \| endOf: MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * 与えられた時間から`unit`で定義された時間帯の`delta`だけ引いた時間を返します。
    * ex\) \{\{ executionTime \| subTime: 10\, MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| subTime: 10\, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 与えられた時間から`unit`で定義された時間帯の`delta`だけ足した時間を返します。
    * ex\) \{\{ executionTime \| addTime: 10\, MINUTE \}\}
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| addTime: 10\, MINUTE \}\}
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
    * ex\) \{\{ 2022\-11\-04T13:31:28Z \| format: 'yyyy' \}\}
        * → 2022
* nested filter例
    * フローの実行が始まった日の03時のDSL表現
        * =\> \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## Source

* フローにデータを取り込むエンドポイントを定義するノードタイプです。

## Sourceノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| タイプ | - | string | 各メッセージに与えられた値で`type`フィールドを作成します。 |  |
| 測定項目の有効化 | true | boolean | ノードのマトリックスを収集します。<br>プロパティ値がtrueの場合、モニタリングタブでノードのイベントマトリックス情報を確認できます。 |  |
| ID | - | string | ノードのIDを設定します。<br>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |
| タグ | - | array of string | 各メッセージに与えられた値のタグを追加します。 |  |
| フィールド追加 | - | hash | カスタムフィールドを追加できます。<br>`%{[depth1_field]}`で各フィールドの値を取得してフィールドを追加できます。 |  |

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
* Log & Crash Searchの[ログ検索API](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/#api_1)のトークンに影響を受けます。
  * トークンが足りない場合はLog & Crash Searchにお問い合わせください。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Log & Crash Searchのアプリケーションキーを入力します。 |  |
| 照会開始時間 | - | string | ログ照会の開始時間を入力します。 | [参考](#dsl) |
| 照会終了時間 | - | string | ログ照会の終了時間を入力します。 |  |

### コーデック別メッセージ取り込み

* Log & Crash Searchは基本的に```JSON```形式のデータを扱います。
    * [参考 - Log & Crash Search APIガイド](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/)
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

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | CloudTrailのアプリケーションキーを入力します。 |  |
| 照会開始時間 | - | string | データ照会の開始時間を入力します。 | [参考](#dsl) |
| 照会終了時間 | - | string | データ照会の終了時間を入力します。 |  |

### コーデック別メッセージ取り込み

* CloudTrailは基本的に```JSON```形式のデータを扱っています。
    * [参考 -CloudTrail APIガイド](https://docs.toast.com/ko/CloudTrail/ko/api-guide/)
* コーデックを選択しない場合、またはplainの場合は、CloudTrailデータのJSON文字列を`message`というフィールドに含めます。
* CloudTrailデータの各フィールドを活用したい場合は、jsonコーデックを使用することを推奨します。

#### 未選択またはplain

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

* 取り込まれたデータをどのように処理するかを定義するノードタイプです。

## Filterノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 測定項目の有効化 | true | boolean | ノードのマトリックスを収集します。<br>プロパティ値がtrueの場合、モニタリングタブでノードのイベントマトリックス情報を確認できます。 |  |
| ID | - | string | ノードのIDを設定します。<br>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |
| タグの追加 | - | array of string | 各メッセージにタグを追加します。 |  |
| タグの削除 | - | array of string | 各メッセージに与えられたタグを削除します。 |  |
| フィールドの削除 | - | array of string | 各メッセージのフィールドを削除します。 |  |
| フィールドの追加 | - | hash | カスタムフィールドを追加できます。<br>`%{[depth1_field]}`で各フィールドの値を取得してフィールドを追加できます。 |  |

## Cipher

### ノードの説明

* メッセージフィールドの値を暗号化または復号するノードです。
* 暗号化キーはSKMを参照します。
    * SKMキー登録の詳細については[SKMガイド文書](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/overview/)をご覧ください。
    * ```1つのフローに複数のCipherノードが含まれていても、すべてのCipherノードは必ず1つのSKMキーリファレンスのみ参照できます。```

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| モード | - | enum | 暗号化モードと復号モードのいずれかを選択します。 | リストから1つを選択します。 |
| アプリケーションキー | - | string | 暗号化/復号に使用するキーを保存したSKMアプリケーションキーを入力します。 |  |
| キーID | - | string | 暗号化/復号に使用するキーを保存したSKMキーIDを入力します。 |  |
| キーバージョン | - | string | 暗号化/復号に使用するキーを保存したSKMキーバージョンを入力します。 |  |
| 暗号化/復号キーの長さ | 16 | positive integer | 暗号化/復号キーの長さを入力します。 |  |
| IVランダム長 | - | positive number | Initial Vectorのrandom bytes長を入力します。 |  |
| ソースフィールド | - | string | 暗号化/復号するフィールド名を入力します。 |  |
| 保存するフィールド | - | string | 暗号化/復号結果を保存するフィールド名を入力します。 |  |

### encrypt例

#### 条件

* mode → `encrypt`
* アプリケーションキー→ `SKMアプリケーションキー`
* キーID → `SKM対称鍵ID`
* キーバージョン→ `1`
* IVランダム長→ `16`
* ソースフィールド→ message
* 保存するフィールド→ encrypted\_message

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
* アプリケーションキー→ `SKMアプリケーションキー`
* キーID → `SKM対称鍵ID`
* キーバージョン→ `1`
* IVランダム長→ `16`
* ソースフィールド→ message
* 保存するフィールド→ decrypted\_message

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

## Sink

* Filter作業が完了したデータをロードするエンドポイントを定義するノードタイプです。

## Sinkノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 測定項目の有効化 | true | boolean | ノードのマトリックスを収集します。<br>プロパティ値がtrueの場合、モニタリングタブでノードのイベントマトリックス情報を確認できます。 |  |
| ID | - | string | ノードのIDを設定します。<br>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |

## (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageにデータをアップロードするノードです。
* OBSに作成されるObjectは、次のパスフォーマットに合わせて出力されます。
    * `/{container_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/ls.s3.{uuid}.{yyyy}-{MM}-{dd}T{HH}.{mm}.part{seq_id}.txt`

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| リージョン | - | enum | Object Storage商品のリージョンを入力します。 | [OBSリージョンの詳細](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#aws-sdk) |
| バケット | - | string | バケット名を入力します。 |  |
| 秘密鍵 | - | string | S3 API認証情報の秘密鍵を入力します。 |  |
| アクセスキー | - | string | S3 API認証情報のアクセスキーを入力します。 |  |
| エンコード | none | enum | エンコードするかどうかを入力します。 gzipエンコードを使用できます。 |  |
| ファイルローテーションポリシー | size\_and\_time | enum | ファイルの作成ルールを決定します。 | size\_and\_time - ファイルのサイズと時間を利用して決定<br>size - ファイルのサイズを利用して決定<br>time - 時間を利用して決定 |
| 基準時刻 | 15 | number | ファイルを分割する基準となる時間を設定します。 | ファイルローテーションポリシーがsize\_and\_timeまたはtimeの場合に設定 |
| ファイルサイズ | 5242880 | number | ファイルを分割する基準となるサイズを設定します。 | ファイルローテーションポリシーがsize\_and\_timeまたはsizeの場合に設定 |
| ACL | private | enum | ファイルをアップロードする時に設定するACLポリシーを入力します。 | 
| ストレージクラス | STANDARD | enum | ファイルをアップロードする時に使用するストレージクラスを設定します。 | [ストレージクラスガイドl](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |

### jsonコーデックの出力例

#### 条件

* リージョン→ `KR1`
* バケット→ `obs-test-container`
* アクセスキー→ `******`
* 秘密鍵→ `******`

#### 入力メッセージ

``` json
{"hidden":"Hello DataFlow!","message":"Hello World", "@timestamp": "2022-11-21T07:49:20Z"}
```

#### 出力値

* パス

```
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* 出力メッセージ

``` json
{"@timestamp":"2022-11-21T07:49:20.000Z","host":"755b65d82bd0","hidden":"Hello DataFlow!","@version":"1","sequence":0,"message":"Hello World"}
```

### plainコーデック出力例 - messageフィールド存在する場合

#### 条件

* リージョン→ `KR1`
* バケット→ `obs-test-container`
* アクセスキー→ `******`
* 秘密鍵→ `******`

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
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T07.49.part0.txt
```

* 出力メッセージ

```
2022-11-21T07:49:20.000Z e0e40e03dd94 Hello World
```

### plainコーデック出力例 - messageフィールドが存在しない場合

#### 条件

* リージョン→ `KR1`
* バケット→ `obs-test-container`
* アクセスキー→ `******`
* 秘密鍵→ `******`

#### 入力メッセージ

``` json
{
    "hidden": "Hello DataFlow!",
    "@timestamp": "2022-11-21T07:49:20Z"
}
```

#### 出力値

* パス

```
/obs-test-container/2022/month=11/day=21/hour=07/ls.s3.d53c090b-9718-4833-926a-725b20c85974.2022-11-21T00.47.part0.txt
```

* 出力メッセージ

```
2022-11-21T07:49:20.000Z f207c24a122e %{message}
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
| 秘密鍵 | - | string | S3 API認証情報の秘密鍵を入力します。 |  |
| 署名バージョン | - | enum | AWSリクエストを署名する時に使用するバージョンを入力します。 |  |
| セッショントークン | - | string | AWS一時認証情報のためのセッショントークンを入力します。 | [セッショントークンガイド](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) |
| Prefix | - | string | ファイルをアップロードする時に名前の前につけるプレフィックスを入力します。 |  |
| ストレージクラス | STANDARD | enum | ファイルをアップロードする時に使用するストレージクラスを設定します。 | [ストレージクラスガイド](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |
| エンコード | none | enum | エンコードするかどうかを入力します。gzipエンコードを使用できます。 |  |
| ファイルローテーションポリシー | size\_and\_time | enum | ファイルの作成ルールを決定します。 | size\_and\_time - ファイルのサイズと時間を利用して決定<br>size - ファイルのサイズを利用して決定<br>time - 時間を利用して決定 |
| 基準時刻 | 15 | number | ファイルを分割する基準となる時間を設定します。 | ファイルローテーションポリシーがsize\_and\_timeまたはtimeの場合に設定 |
| ファイルサイズ | 5242880 | number | ファイルを分割する基準となるサイズを設定します。 | ファイルローテーションポリシーがsize\_and\_timeまたはsizeの場合に設定 |
| ACL | private | enum | ファイルをアップロードする時に設定するACLポリシーを入力します。 |  |
| 追加設定 | { } | hash | S3に接続するための追加設定を入力します。 | [ガイド](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html) |

### 出力例

* OBSと同じです。

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

## Kafka

### ノードの説明

* Kafkaにデータを転送するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| トピック | - | string | メッセージを転送するKafkaトピック名を入力します。 |  |
| ブローカーサーバーリスト | localhost:9092 | string | Kafkaブローカーサーバーを入力します。サーバーが複数の場合はコンマ(`,`)で区切ります。 | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| クライアントID | dataflow | string | Kafka Producerを識別するIDを入力します。 | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| メッセージシリアライズタイプ | org.apache.kafka.common.serialization.StringSerializer | string | 転送するメッセージの値をシリアライズする方法を入力します。 | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| 圧縮タイプ | none | enum | 転送するデータを圧縮する方法を入力します。 | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br>none、gzip、snappy、lz4から選択 |
| キーシリアライズタイプ | org.apache.kafka.common.serialization.StringSerializer | string | 転送するメッセージのキーをシリアライズする方法を入力します。 | [key.serializer](https://kafka.apache.org/documentation/#producerconfigs_key.serializer) |
| メタデータ更新周期 | 300000 | number | パーティション、ブローカーサーバー状態などを更新する周期(ms)を入力します。 | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms) |
| 最大リクエストサイズ | 1048576 | number | 転送リクエストごとの最大サイズ(byte)を入力します。 | [max.request.size](https://kafka.apache.org/documentation/#producerconfigs_max.request.size) |
| サーバー再接続周期 | 50 | number | ブローカーサーバーに接続が失敗した時に再試行する周期を入力します。 | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms) |
| バッチサイズ | 16384 | number | バッチリクエストで転送するサイズ(byte)を入力します。 | [batch.size](https://kafka.apache.org/documentation/#producerconfigs_batch.size) |
| バッファメモリ | 33554432 | number | Kafka転送に使用するバッファのサイズ(byte)を入力します。 | [buffer.memory](https://kafka.apache.org/documentation/#producerconfigs_buffer.memory) |
| 受信バッファサイズ | 32768 | number | データの読み込みに使用するTCP receiveバッファのサイズ(byte)を入力します。 | [receive.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_receive.buffer.bytes) |
| 転送遅延時間 | 0 | number | メッセージ転送を遅らせる時間を入力します。遅延したメッセージはバッチリクエストで一度に転送します。 | [linger.ms](https://kafka.apache.org/documentation/#producerconfigs_linger.ms) |
| サーバーリクエストタイムアウト | 40000 | number | 転送リクエストのタイムアウト(ms)を入力します。 | [request.timeout.ms](https://kafka.apache.org/documentation/#producerconfigs_request.timeout.ms) |
| メタデータ照会タイムアウト |  | number |  | [https://kafka.apache.org/documentation/#upgrade\_1100\_notable](https://kafka.apache.org/documentation/#upgrade_1100_notable) |
| 転送バッファサイズ | 131072 | number | データを転送するのに使用するTCP sendバッファのサイズ(byte)を入力します。 | [send.buffer.bytes](https://kafka.apache.org/documentation/#producerconfigs_send.buffer.bytes) |
| ackプロパティ | 1 | enum | ブローカーサーバーでメッセージを受信したことを確認する設定を入力します。 | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br>0 - メッセージを受信したかどうかを確認しません。<br>1 - トピックのleaderが、followerがデータをコピーすることを待たずにメッセージを受信したというレスポンスを返します。<br>all - トピックのleaderが、followerがデータをコピーするのを待ってからメッセージを受信したというレスポンスを返します。 |
| リクエストの再接続周期 | 100 | number | 転送リクエストが失敗した時に再試行する周期(ms)を入力します。 | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms) |
| 再試行回数 | - | number | 転送リクエストが失敗した時に再試行する最大回数を入力します。 | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br>設定値を超えて再試行すると、データが消失する可能性があります。 |

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

* 条件文→ `[logLevel] == "ERROR"`

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

* 条件文→ `[response][status] == 200`

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
