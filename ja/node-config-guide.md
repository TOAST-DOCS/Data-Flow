## Data & Analytics > DataFlow > ノードタイプガイド

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
    * 与えられた時間から`unit`で定義された時間帯の最後の時間を返します。
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
    * 与えられた時間から`unit`で定義された時間帯の`delta`だけ引いた時間を返します。
    * ex\) \{\{ executionTime \| subTime: 10\, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10\, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 与えられた時間から`unit`で定義された時間帯の`delta`だけ足した時間を返します。
    * ex\) \{\{ executionTime \| addTime: 10\, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10\, MINUTE \}\}
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

## Source

* フローにデータを取り込むエンドポイントを定義するノードタイプです。

## Sourceノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| タイプ | - | string | 各メッセージに与えられた値で`type`フィールドを作成します。 |  |
| 測定項目の有効化 | true | boolean | ノードのマトリックスを収集します。<br/>プロパティ値がtrueの場合、モニタリングタブでノードのイベントマトリックス情報を確認できます。 |  |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |
| タグ | - | array of string | 各メッセージに与えられた値のタグを追加します。 |  |
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
* Log & Crash Searchの[ログ検索API](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/api-guide/#api_1)のトークンに影響を受けます。
  * トークンが足りない場合はLog & Crash Searchにお問い合わせください。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Log & Crash Searchのアプリケーションキーを入力します。 |  |
| 照会開始時間 | - | string | ログ照会の開始時間を入力します。 | [参考](#dsl) |
| 照会終了時間 | - | string | ログ照会の終了時間を入力します。 |  |

* 照会開始時間と照会終了時間の設定
    * 照会終了時間がフロー実行時点より遅い場合でも、フローは照会終了時間まで待機せず、現在照会できるデータのみ照会した後に終了します。

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

* 照会開始時間と照会終了時間の設定
    * 照会終了時間がフロー実行時点より遅い場合でも、フローは照会終了時間まで待機せず、現在照会できるデータのみ照会した後に終了します。

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

## Source > (NHN Cloud) OBS

### ノードの説明

* NHN CloudのObject Storageからデータの入力を受け取るノードです。
* オブジェクト作成時間を基準に最も早く作成されたオブジェクトからデータを読み込みます。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| バケット | - | string | データを読み込むバケット名を入力します。 |  |
| リージョン | - | string | リポジトリに設定されたリージョン情報を入力します。 |  |
| 秘密鍵 | - | string | S3が発行した認証情報秘密鍵を入力します。 |  |
| アクセスキー | - | string | S3が発行した認証情報アクセスキーを入力します。 |  |
| リスト更新周期 | - | number | バケットに含まれるオブジェクトリスト更新周期を入力します。 |  |
| 新しいファイルをチェックするかどうか | - | boolean | プロパティ値がtrueの場合、新しく追加されるオブジェクトも一緒に読み込みます。 |  |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 |  |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 |  |
| 削除 | false | boolean | プロパティ値がtrueの場合、読み込みが完了したオブジェクトを削除します。 |  |

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

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| エンドポイント | - | string | S3リポジトリエンドポイントを入力します。 | HTTP、HTTPS URL形式のみ入力可能です。 |
| バケット | - | string | データを読み込むバケット名を入力します。 |  |
| リージョン | - | string | リポジトリに設定されたリージョン情報を入力します。 |  |
| セッショントークン | - | string | AWSセッショントークンを入力します。 |  |
| 秘密鍵 | - | string | S3が発行した認証情報秘密鍵を入力します。 |  |
| アクセスキー | - | string | S3が発行した認証情報アクセスキーを入力します。 |  |
| リスト更新周期 | - | number | バケットに含まれるオブジェクトリスト更新周期を入力します。 |  |
| メタ情報を含めるかどうか | - | boolean | S3オブジェクトのメタデータをキーとして含めるかどうかを決定します。 | (最後の修正時間、 Content-Typeなど) |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 |  |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 |  |
| 追加設定 | - | hash | S3サーバーと接続する時に使用する追加設定を入力します。 | 使用可能な設定の全リストは次のリンクをご覧ください。<br/>https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/Client.html<br/>例)<br/>{<br/>"force\_path\_style": true<br/>} |

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

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ブローカーサーバーリスト | localhost:9092 | string | Kafkaブローカーサーバーを入力します。サーバーが複数台の場合はコンマ(`,`)で区切ります。 | [bootstrap.servers](https://kafka.apache.org/documentation/#consumerconfigs_bootstrap.servers)<br/>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| コンシューマーグループID | dataflow | string | Kafka Consumer Groupを識別するIDを入力します。 | [group.id](https://kafka.apache.org/documentation/#consumerconfigs_group.id) |
| 内部トピックを除外するかどうか | true | boolean |  | [exclude.internal.topics](https://kafka.apache.org/documentation/#consumerconfigs_exclude.internal.topics)<br/>受信対象から`__consumer_offsets`などの内部トピックを除外します。 |
| トピックパターン | - | string | メッセージを受信するKafkaトピックパターンを入力します。 | ex) `*-messages` |
| クライアントID | dataflow | string | Kafka Consumerを識別するIDを入力します。 | [client.id](https://kafka.apache.org/documentation/#consumerconfigs_client.id) |
| パーティション割り当てポリシー | - | string | Kafkaからメッセージを受信した時にコンシューマーグループにどのようにパーティションを割り当てるかを決定します。 | [partition.assignment.strategy](https://kafka.apache.org/documentation/#consumerconfigs_partition.assignment.strategy)<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| オフセット設定 | none | enum | コンシューマーグループのオフセットを設定する基準を入力します。 | [auto.offset.reset](https://kafka.apache.org/documentation/#consumerconfigs_auto.offset.reset)<br/>以下の設定はすべて、コンシューマーグループがすでに存在する場合は既存オフセットを維持します。<br/>none：コンシューマーグループがない場合はエラーを返します。<br/>earliest：コンシューマーグループがない場合はパーティションの最も古いオフセットで初期化します。<br/>latest：コンシューマーグループがない場合はパーティションの最も新しいオフセットで初期化します。 |
| オフセットコミット周期 | 5000 | number | コンシューマーグループのオフセットを更新する周期を入力します。 | [auto.commit.internal.ms](https://kafka.apache.org/documentation/#consumerconfigs_auto.commit.interval.ms) |
| オフセット自動コミットするかどうか | true | boolean |  | [enable.auto.commit](https://kafka.apache.org/documentation/#consumerconfigs_enable.auto.commit) |
| キー逆シリアル化タイプ | org.apache.kafka.common.serialization.StringDeserializer | string | 受信するメッセージのキーをシリアライズする方法を入力します。 | [key.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_key.deserializer) |
| メッセージ逆シリアル化タイプ | org.apache.kafka.common.serialization.StringDeserializer | string | 受信するメッセージの値をシリアライズする方法を入力します。 | [value.deserializer](https://kafka.apache.org/documentation/#consumerconfigs_value.deserializer) |
| メタデータを作成するかどうか | false | boolean | プロパティ値がtrueの場合、メッセージのメタデータフィールドを作成します。メタデータフィールドを活用するにはfilterノードタイプを組み合わせる必要があります(下のガイド参照)。 | 作成されるフィールドは次のとおりです。<br/>topic：メッセージを受信したトピック<br/>consumer\_group：メッセージを受信するために使用したコンシューマーグループID<br/>partition：メッセージを受信したトピックのパーティション番号<br/>offset：メッセージを受信したパーティションのオフセット<br/>key：メッセージキーを含むByteBuffer |
| Fetch最小サイズ | - | number | 1回のfetchリクエストで取得するデータの最小サイズを入力します。 | [fetch.min.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.min.bytes) |
| 転送バッファサイズ | - | number | データの転送に使用するTCP sendバッファのサイズ(byte)を入力します。 | [send.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_send.buffer.bytes) |
| 再試行リクエスト周期 | 100 | number | 転送リクエストが失敗した時に再試行する周期(ms)を入力します。 | [retry.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_retry.backoff.ms) |
| 循環重複検査 | true | enum | メッセージのCRCを検査します。 | [check.crcs](https://kafka.apache.org/documentation/#consumerconfigs_check.crcs) |
| サーバー再接続周期 | 50 | number | ブローカーサーバーに接続が失敗した時に再試行する周期を入力します。 | [reconnect.backoff.ms](https://kafka.apache.org/documentation/#consumerconfigs_reconnect.backoff.ms) |
| Pollタイムアウト | 100 | number | トピックで新しいメッセージを取得するリクエストのタイムアウト(ms)を入力します。 |  |
| パーティションあたりのFetch最大サイズ | - | number | パーティションあたり1回のfetchリクエストで取得する最大サイズを入力します。 | [max.partition.fetch.bytes](https://kafka.apache.org/documentation/#consumerconfigs_max.partition.fetch.bytes) |
| サーバーリクエストタイムアウト | 30000 | number | 転送リクエストに対するタイムアウト(ms)を入力します。 | [request.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_request.timeout.ms) |
| TCP受信バッファサイズ | - | number | データの読み込みに使用するTCP receiveバッファのサイズ(byte)を入力します。 | [receive.buffer.bytes](https://kafka.apache.org/documentation/#consumerconfigs_receive.buffer.bytes) |
| session\_timeout\_ms | - | number | コンシューマーのセッションタイムアウト(ms)を入力します。<br/>コンシューマーがその時間内にheartbeatを送信できなかった場合はコンシューマーグループから除外します。 | [session.timeout.ms](https://kafka.apache.org/documentation/#consumerconfigs_session.timeout.ms) |
| 最大pollメッセージ数 | - | number | 1回のpollリクエストで取得する最大メッセージ数を入力します。 | [max.poll.records](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.records) |
| 最大poll周期 | - | number | pollリクエスト間の最大周期(ms)を入力します。 | [max.poll.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_max.poll.interval.ms) |
| Fetch最大サイズ | - | number | 1回のfetchリクエストで取得する最大サイズを入力します。 | [fetch.max.bytes](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.bytes) |
| Fetch最大待機時間 | - | number | `Fetch最小サイズ`設定データが集まらない場合はfetchリクエストを送る待機時間(ms)を入力します。 | [fetch.max.wait.ms](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.wait.ms) |
| コンシューマーヘルスチェック周期 | - | number | コンシューマーがheartbeatを送る周期(ms)を入力します。 | [heartbeat.interval.ms](https://kafka.apache.org/documentation/#consumerconfigs_heartbeat.interval.ms) |
| メタデータ更新周期 | - | number | パーティション、ブローカーサーバーの状態などを更新する周期(ms)を入力します。 | [metadata.max.age.ms](https://kafka.apache.org/documentation/#producerconfigs_metadata.max.age.ms) |
| IDLEタイムアウト | - | number | データ転送がないコネクションを閉じる待機時間(ms)を入力します。 | [connections.max.idle.ms](https://kafka.apache.org/documentation/#consumerconfigs_connections.max.idle.ms) |

### メタデータフィールドの使用方法

* `メタデータを作成するかどうか`設定を有効にするとメタデータフィールドが作成されますが、別途一般フィールドに注入する作業を行っていなければFilter、Sinkなどのプラグインで表示しません。
* 設定を有効にした時のKafkaプラグイン以降のメッセージ例
    ``` js
    {
        // 一般フィールド
        "@version": "1",
        "@timestamp": "2022-04-11T00:01:23Z"
        "message": "kafkaトピックメッセージ。.."
        // メタデータフィールド
        // ユーザーが一般フィールドに注入するまで活用不可
        // "[@metadata][kafka][topic]": "my-topic"
        // "[@metadata][kafka][consumer_group]": "my_consumer_group"
        // "[@metadata][kafka][partition]": "1"
        // "[@metadata][kafka][offset]": "123"
        // "[@metadata][kafka][key]": "my_key"
        // "[@metadata][kafka][timestamp]": "-1"
    }
    ```

* Kafka Sourceプラグインにフィールド追加オプションが存在しますが、データの取り込みと同時にフィールド追加作業を行えません。
* **任意のFilterプラグイン**の共通設定にあるフィールド追加オプションを利用して一般フィールドとして追加します。
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
        ``` js
        {
            // 一般フィールド
            "@version": "1",
            "@timestamp": "2022-04-11T00:01:23Z"
            "message": "kafkaトピックメッセージ。.."
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

## Filter
* 取り込まれたデータをどのように処理するかを定義するノードタイプです。

## Filterノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 測定項目の有効化 | true | boolean | ノードのマトリックスを収集します。<br/>プロパティ値がtrueの場合、モニタリングタブでノードのイベントマトリックス情報を確認できます。 |  |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |
| タグの追加 | - | array of string | 各メッセージにタグを追加します。 |  |
| タグの削除 | - | array of string | 各メッセージに与えられたタグを削除します。 |  |
| フィールドの削除 | - | array of string | 各メッセージのフィールドを削除します。 |  |
| フィールド追加 | - | hash | カスタムフィールドを追加できます。<br/>`%{[depth1_field]}`で各フィールドの値を取得してフィールドを追加できます。 |  |

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
| パターン定義 | - | hash | 解析するトークンのルールのユーザー定義パターンを正規表現で入力します。 | システム定義パターンについては以下のリンクをご確認ください。<br/>http://grokdebug.herokuapp.com/patterns |
| 失敗タグ | - | array of strings | 文字列の解析に失敗した場合に定義するタグ名を入力します。 |  |
| タイムアウト | 30000 | numeric | 文字列の解析が完了するまでの待機時間を入力します。 |  |
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
| タイムアウト | 30000 | numeric | 文字列の解析が行われるまでの待機時間を入力します。 |  |
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
| Match | - | array of strings | 文字列を取得するためのフィールド名とフォーマットを入力します。 |  |
| 保存するフィールド | - | string | Date文字列解析結果を保存するフィールド名を入力します。 |  |
| 失敗タグ | - | array of strings | Date文字列の解析に失敗した場合に定義するタグ名を入力します。 |  |
| 時間帯 | - | string | 日付の時間帯を入力します。 |  |

### Date文字列の解析例

#### 条件

* Match → `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* 保存するフィールド → `time`
* 時間帯 → `Asia/Seoul`

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

## Sink

* Filter作業が完了したデータをロードするエンドポイントを定義するノードタイプです。

## Sinkノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 測定項目の有効化 | true | boolean | ノードのマトリックスを収集します。<br/>プロパティ値がtrueの場合、モニタリングタブでノードのイベントマトリックス情報を確認できます。 |  |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表記します。 |  |

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
| ファイルローテーションポリシー | size\_and\_time | enum | ファイルの作成ルールを決定します。 | size\_and\_time - ファイルのサイズと時間を利用して決定<br/>size - ファイルのサイズを利用して決定<br/>time - 時間を利用して決定 |
| 基準時刻 | 15 | number | ファイルを分割する基準となる時間を設定します。 | ファイルローテーションポリシーがsize\_and\_timeまたはtimeの場合に設定 |
| ファイルサイズ | 5242880 | number | ファイルを分割する基準となるサイズを設定します。 | ファイルローテーションポリシーがsize\_and\_timeまたはsizeの場合に設定 |
| ACL | private | enum | ファイルをアップロードする時に設定するACLポリシーを入力します。 | 
| ストレージクラス | STANDARD | enum | ファイルをアップロードする時に使用するストレージクラスを設定します。 | [ストレージクラスガイドl](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) |

### jsonコーデックの出力例

#### 条件

* リージョン → `KR1`
* バケット → `obs-test-container`
* アクセスキー → `******`
* 秘密鍵 → `******`

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

* リージョン → `KR1`
* バケット → `obs-test-container`
* アクセスキー → `******`
* 秘密鍵 → `******`

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

* リージョン → `KR1`
* バケット → `obs-test-container`
* アクセスキー → `******`
* 秘密鍵 → `******`

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
| ファイルローテーションポリシー | size\_and\_time | enum | ファイルの作成ルールを決定します。 | size\_and\_time - ファイルのサイズと時間を利用して決定<br/>size - ファイルのサイズを利用して決定<br/>time - 時間を利用して決定 |
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
| ブローカーサーバーリスト | localhost:9092 | string | Kafkaブローカーサーバーを入力します。サーバーが複数の場合はコンマ(`,`)で区切ります。 | [bootstrap.servers](https://kafka.apache.org/documentation/#producerconfigs_bootstrap.servers)<br/>ex) 10.100.1.1:9092,10.100.1.2:9092 |
| クライアントID | dataflow | string | Kafka Producerを識別するIDを入力します。 | [client.id](https://kafka.apache.org/documentation/#producerconfigs_client.id) |
| メッセージシリアライズタイプ | org.apache.kafka.common.serialization.StringSerializer | string | 転送するメッセージの値をシリアライズする方法を入力します。 | [value.serializer](https://kafka.apache.org/documentation/#producerconfigs_value.serializer) |
| 圧縮タイプ | none | enum | 転送するデータを圧縮する方法を入力します。 | [compression.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)<br/>none、gzip、snappy、lz4から選択 |
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
| ackプロパティ | 1 | enum | ブローカーサーバーでメッセージを受信したことを確認する設定を入力します。 | [acks](https://kafka.apache.org/documentation/#producerconfigs_acks)<br/>0 - メッセージを受信したかどうかを確認しません。<br/>1 - トピックのleaderが、followerがデータをコピーすることを待たずにメッセージを受信したというレスポンスを返します。<br/>all - トピックのleaderが、followerがデータをコピーするのを待ってからメッセージを受信したというレスポンスを返します。 |
| リクエストの再接続周期 | 100 | number | 転送リクエストが失敗した時に再試行する周期(ms)を入力します。 | [retry.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_retry.backoff.ms) |
| 再試行回数 | - | number | 転送リクエストが失敗した時に再試行する最大回数を入力します。 | [retries](https://kafka.apache.org/documentation/#producerconfigs_retries)<br/>設定値を超えて再試行すると、データが消失する可能性があります。 |

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
