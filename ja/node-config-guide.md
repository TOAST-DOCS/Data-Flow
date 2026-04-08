## Data & Analytics > DataFlow > ノード設定ガイド

* ノードタイプは、簡単にフローを作成できるように事前定義されたテンプレートです。
* ノードタイプの種類は、Source、Filter、Branch、Sinkです。
* Source、Sinkノードタイプは、必ずテストを実行してエンドポイント情報が有効かどうかを確認することを推奨します。
* アクセス制御が設定されたデータソースの接続時には、DataFlow IP固定機能を使用する必要があります。
    * DataFlow IP固定機能を使用するには、**カスタマーサポート > お問い合わせ**よりお問い合わせください。

### Object Storage接続時の注意点
リージョンまたはプロジェクトが異なるObject Storageであっても、バケット名が同一の場合は、1つのフローで一緒に使用することはできません。

!!! tip "不可能な接続設定の例"
    * 例1
        * 1番目の接続対象Object Storage情報
            * リージョン: KR1
            * バケット名: Data
            * プロジェクト: TEST
        * 2番目の接続対象Object Storage情報
            * リージョン: JP1
            * バケット名: Data
            * プロジェクト: TEST
        * リージョンが異なるため、2つのバケットは異なるバケットですが、DataFlowのフローでは一緒に使用不可
    * 例2
        * 1番目の接続対象Object Storage情報
            * リージョン: KR1
            * バケット名: Data
            * プロジェクト: TEST_1
        * 2番目の接続対象Object Storage情報
            * リージョン: KR1
            * バケット名: Data
            * プロジェクト: TEST_2
        * プロジェクトが異なるため、2つのバケットは異なるバケットですが、DataFlowのフローでは一緒に使用不可


## Domain Specific Language(DSL)の定義

* フロー実行に必要なDSLの定義です。

### Variable

* `{{ executionTime }}`
    * フロー実行時間
* 時間単位 ( unit )
    * 分 - `{{ MINUTE }}`
    * 時 - `{{ HOUR }}`
    * 日 - `{{ DAY }}`
    * 月 - `{{ MONTH }}`
    * 年 - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * 指定された時間から`unit`で定義された時間帯の開始時間を返します。
    * [注意] 韓国時間を基準に計算します。
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
    * 指定された時間から`unit`で定義された時間帯の最後の時間を返します。
    * [注意] 韓国時間を基準に計算します。
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
    * 指定された時間から`unit`で定義された時間帯の`delta`分だけ引いた時間を返します。
    * ex\) \{\{ executionTime \| subTime: 10, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 指定された時間から`unit`で定義された時間帯の`delta`分だけ足した時間を返します。
    * ex\) \{\{ executionTime \| addTime: 10, MINUTE \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10, MINUTE \}\}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * 指定された時間を`formatStr`形式で返します。
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
    * ex\) \{\{ executionTime \| format: 'yyyy' \}\}
    * ex\) \{\{ "2022\-11\-04T13:31:28Z" \| format: 'yyyy' \}\}
        * → 2022
* nested filterの例
    * フロー実行が開始された日の03時のDSL表現
        * → \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## データ型別の入力方法
### string
* 文字列を入力します。

### number
* 0以上の数値を入力します。
* 入力ウィンドウ右側の矢印を使用して、値を1ずつ調整できます。

### boolean
* プルダウンから`TRUE`または`FALSE`を選択します。

### enum
* プルダウンから項目を選択します。

### array of strings
* 配列に入る文字列を1つずつ入力します。
* 文字列を入力した後、`+`ボタンをクリックすると配列に文字列が挿入されます。
* 例: `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`を入力したい場合、`message`、`yyyy-MM-dd HH:mm:ssZ`、`ISO8601`の順に配列に文字列を挿入します。

### hash
* JSON形式の文字列を入力します。

## Source

* フローにデータを取り込むエンドポイントを定義するノードタイプです。

### 実行モード

* Sourceノードには実行モードが存在し、BATCHモードとSTREAMINGモードに分かれます。
    * STREAMINGモード: フローを終了せずにリアルタイムでデータを処理します。
    * BATCHモード: 決められたデータを処理した後、フローを終了します。
* Sourceノードごとにサポートする実行モードが異なります。

### Sourceノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表示します。 |  |

## Source > (NHN Cloud) Log & Crash Search

### ノードの説明

* (NHN Cloud) Log & Crash Searchノードは、Log & Crash Searchからログを読み込むノードです。
* ノードにログ照会開始時間を設定できます。設定しない場合、フローを開始した時点からログを読み込みます。
* ノードに終了時間を入力しない場合、ストリーミング形式でログを読み込みます。終了時間を入力すると、終了時間までのログを読み込んでフローは終了します。
* ```現在のセッションログとクラッシュログはサポートしていません。```
* Log & Crash Searchのログ検索APIのトークンに影響を受けます。
    * トークンが不足している場合は、Log & Crash Searchにお問い合わせください。

### 実行モード
* STREAMING: `照会開始時間`以降のデータを継続して処理します。
* BATCH: `照会開始時間`、`照会終了時間`の間に該当するデータを全て処理し、フローを終了します。


### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|-----------|---------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey | - | string | Log & Crash SearchのAppkeyを入力します。 | |
| SecretKey | - | string | Log & Crash Searchのシークレットキーを入力します。 | |
| 照会開始時間 | `{{executionTime}}` | string | ログ照会の開始時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} | |
| 照会終了時間 | - | string | ログ照会の終了時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} | |
| 検索クエリ | `*` | string | Log & Crash Searchの照会リクエスト時に使用する検索クエリを入力します。詳細なクエリの作成方法は、Log & Crash Searchサービスの「Luceneクエリガイド」を参考にしてください。 | |

### コーデック別のメッセージ取り込み

* Log & Crash Searchは基本的にJSON形式のデータを扱います。
* Log & Crash Searchログの各フィールドを活用したい場合は、jsonコーデックを使用することを推奨します。

**サポートするコーデック:**
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (NHN Cloud) CloudTrail

### ノードの説明

* (NHN Cloud) CloudTrailは、CloudTrailからデータを読み込むノードです。
* ノードにデータ照会開始時間を設定できます。設定しない場合、フローを開始した時点からデータを読み込みます。
* ノードに終了時間を入力しない場合、ストリーミング形式でデータを読み込みます。終了時間を入力すると、終了時間までのデータを読み込んでフローは終了します。

### 実行モード
* STREAMING: `照会開始時間`以降のデータを継続して処理します。
* BATCH: `照会開始時間`、`照会終了時間`の間に該当するデータを全て処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|--------------------|---------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey | - | string | CloudTrailのAppkeyを入力します。 | |
| User Access Key ID | - | string | ユーザーアカウントのUser Access Key IDを入力します。 | |
| Secret Access Key | - | string | ユーザーアカウントのUser Secret Keyを入力します。 | |
| 照会開始時間 | `{{executionTime}}` | string | データ照会の開始時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} | |
| 照会終了時間 | - | string | データ照会の終了時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} | |
| イベントタイプ | `*` | string | 照会するイベントIDを入力します。 | |

### コーデック別のメッセージ取り込み

* CloudTrailは基本的にJSON形式のデータを扱います。
* CloudTrailデータの各フィールドを活用したい場合は、jsonコーデックを使用することを推奨します。

**サポートするコーデック:**
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageからデータを受け取るノードです。
* オブジェクト作成時間を基準に、最も早く作成されたオブジェクトからデータを読み込みます。

### 実行モード
* STREAMING: `リスト更新周期`ごとにオブジェクト一覧を更新し、新たに追加されたオブジェクトを読み込んでデータを処理します。
* BATCH: フロー開始時点にオブジェクト一覧を一度呼び出した後、オブジェクトを読み込んでデータを処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- |---------| --- | --- | --- |
| バケット | - | string | データを読み込むバケット名を入力します。 | |
| リージョン | - | string | ストレージに設定されたリージョン情報を入力します。 | |
| シークレットキー | - | string | S3が発行した資格情報のシークレットキーを入力します。 | |
| アクセスキー | - | string | S3が発行した資格情報のアクセスキーを入力します。 | |
| リスト更新周期 | `60` | number | バケットに含まれるオブジェクト一覧の更新周期を入力します。 | |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 | |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 | |

### コーデック別のメッセージ取り込み

**サポートするコーデック:**
* [plainコーデック](./codec-config-guide.md#plain) - 元データ文字列の保存
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (Amazon) S3

### ノードの説明

* S3からデータを受け取るノードです。
* オブジェクト作成時間を基準に、最も早く作成されたオブジェクトからデータを読み込みます。

### 実行モード
* STREAMING: `リスト更新周期`ごとにオブジェクト一覧を更新し、新たに追加されたオブジェクトを読み込んでデータを処理します。
* BATCH: フロー開始時点にオブジェクト一覧を一度更新した後、オブジェクトを読み込んでデータを処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|---------------|--------------------------------|---------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| エンドポイント | - | string | S3ストレージのエンドポイントを入力します。 | HTTP、HTTPS URL形式のみ入力可能です。 |
| バケット | - | string | データを読み込むバケット名を入力します。 | |
| リージョン | - | string | ストレージに設定されたリージョン情報を入力します。 | |
| シークレットキー | - | string | S3が発行した資格情報のシークレットキーを入力します。 | |
| アクセスキー | - | string | S3が発行した資格情報のアクセスキーを入力します。 | |
| リスト更新周期 | `60` | number | バケットに含まれるオブジェクト一覧の更新周期を入力します。 | |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 | |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 | |
| パス方式リクエスト | `false` | boolean | パス方式リクエストを使用するかどうかを決定します。 | |

!!! danger "注意"
    * (Amazon) S3ノードを使用してNHN Cloud Object Storageに接続する場合、**パス方式リクエスト**を`true`に設定する必要があります。


### コーデック別のメッセージ取り込み

**サポートするコーデック:**
* [plainコーデック](./codec-config-guide.md#plain) - 元データ文字列の保存
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (Apache) Kafka

### ノードの説明

* Kafkaからデータを受信するノードです。

### 実行モード
* STREAMING: トピックに新しいメッセージが到着するたびにデータを処理します。

!!! danger "注意"
    * KafkaノードはBATCHモードをサポートしていません。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|------------------|-----------------------------------|------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ブローカーサーバー一覧 | - | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はカンマ(`,`)で区切ります。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`bootstrap.servers`プロパティを参照 <br/>例: 10.100.1.1:9092,10.100.1.2:9092 |
| コンシューマーグループID | `dataflow` | string | Kafka Consumer Groupを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`group.id`プロパティを参照 |
| トピック一覧 | - | array of strings | メッセージを受信するKafkaトピック一覧を入力します。 | |
| トピックパターン | - | string | メッセージを受信するKafkaトピックパターンを入力します。 | 例: `*-messages` |
| 内部トピック除外の有無 | `true` | boolean | __consumer_offsetsなどの内部トピックを除外します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`exclude.internal.topics`プロパティを参照 <br/>受信対象から`__consumer_offsets`などの内部トピックを除外します。 |
| クライアントID | `dataflow` | string | Kafka Consumerを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`client.id`プロパティを参照 |
| パーティション割り当てポリシー | `["RANGE", "COOPERATIVE_STICKY"]` | array of strings | Kafkaからのメッセージ受信時、コンシューマーグループにどのようにパーティションを割り当てるかを決定します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`partition.assignment.strategy`プロパティを参照 <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| オフセット設定 | `latest` | enum | コンシューマーグループのオフセットを設定する基準を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`auto.offset.reset`プロパティを参照 <br/>以下の設定は全て、コンシューマーグループが既に存在する場合、既存のオフセットを維持します。<br/>none: コンシューマーグループがない場合はエラーを返します。<br/>earliest: コンシューマーグループがない場合は、パーティションの最も古いオフセットで初期化します。<br/>latest: コンシューマーグループがない場合は、パーティションの最新のオフセットで初期化します。 |
| キーのデシリアライズタイプ | `STRING` | enum | 受信するメッセージのキーのタイプを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`key.deserializer`プロパティを参照 |
| メタデータ作成の有無 | `false` | boolean | プロパティ値がtrueの場合、メッセージに対するメタデータフィールドを作成します。メタデータは`kafka_metadata`フィールドに作成されます。 | 作成されるフィールドは次のとおりです。<br/>topic: メッセージを受信したトピック<br/>groupId: メッセージの受信に使用したコンシューマーグループID<br/>partition: メッセージを受信したトピックのパーティション番号<br/>offset: メッセージを受信したパーティションのオフセット<br/>key: メッセージキー |
| Fetch最小サイズ | `1` | number | 1回のfetchリクエストで取得するデータの最小サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.min.bytes`プロパティを参照 |
| 送信バッファーサイズ | `131072` | number | データを送信するために使用するTCP sendバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`send.buffer.bytes`プロパティを参照 |
| 再試行リクエスト周期 | `100` | number | 送信リクエストが失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`retry.backoff.ms`プロパティを参照 |
| 巡回冗長検査 | `true` | boolean | メッセージのCRCを検査します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`check.crcs`プロパティを参照 |
| サーバー再接続周期 | `50` | number | ブローカーサーバーへの接続に失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`reconnect.backoff.ms`プロパティを参照 |
| パーティションごとのFetch最大サイズ | `1048576` | number | パーティションごとに1回のfetchリクエストで取得する最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.partition.fetch.bytes`プロパティを参照 |
| サーバーリクエストタイムアウト | `30000` | number | 送信リクエストに対するタイムアウト(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`request.timeout.ms`プロパティを参照 |
| TCP受信バッファーサイズ | `65536` | number | データの読み込みに使用するTCP receiveバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`receive.buffer.bytes`プロパティを参照 |
| セッションタイムアウト | `45000` | number | コンシューマーのセッションタイムアウト(ms)を入力します。<br/>コンシューマーが該当時間内にheartbeatを送信できない場合、コンシューマーグループから除外します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`session.timeout.ms`プロパティを参照 |
| 最大pollメッセージ数 | `500` | number | 1回のpollリクエストで取得する最大メッセージ数を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.poll.records`プロパティを参照 |
| 最大poll周期 | `300000` | number | pollリクエスト間の最大周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.poll.interval.ms`プロパティを参照 |
| Fetch最大サイズ | `52428800` | number | 1回のfetchリクエストで取得する最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.max.bytes`プロパティを参照 |
| Fetch最大待機時間 | `500` | number | `Fetch最小サイズ`設定分のデータが集まらない場合に、fetchリクエストを送信する待機時間(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.max.wait.ms`プロパティを参照 |
| コンシューマーヘルスチェック周期 | `3000` | number | コンシューマーがheartbeatを送信する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`heartbeat.interval.ms`プロパティを参照 |
| メタデータ更新周期 | `300000` | number | パーティション、ブローカーサーバーの状態などを更新する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`metadata.max.age.ms`プロパティを参照 |
| IDLEタイムアウト | `540000` | number | データ送信がないコネクションを閉じる待機時間(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`connections.max.idle.ms`プロパティを参照 |
| 追加設定 | - | hash | Kafka接続に使用する追加のConsumer設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)を参照 |

### コーデック別のメッセージ取り込み

**サポートするコーデック:**
* [plainコーデック](./codec-config-guide.md#plain) - 元データ文字列の保存
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Filter

* 取り込んだデータをどのように処理するかを定義するノードタイプです。

### Filterノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表示します。 |  |

## Filter > Cipher

### ノードの説明

* メッセージフィールド値を暗号化/復号するノードです。
* 暗号化キーはSecure Key Managerの共通鍵を参照します。
    * Secure Key Managerの共通鍵は、Secure Key Manager Webコンソール、またはSecure Key Managerのキー追加APIを通じて作成できます。
    * 1つのフローに複数のCipherノードが含まれる場合でも、全てのCipherノードは必ず1つのSecure Key Managerのキーリファレンスのみを参照できます。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|--------|---------|---------|-------------------------------------------|------------------|
| モード | - | enum | 暗号化モードと復号モードのいずれかを選択します。 | リストの中から1つを選択します。 |
| Appkey | - | string | 暗号化/復号に使用するキーを保存したSKM Appkeyを入力します。 | |
| キーID | - | string | 暗号化/復号に使用するキーを保存したSKMのキーIDを入力します。 | |
| キーバージョン | - | string | 暗号化/復号に使用するキーを保存したSKMのキーバージョンを入力します。 | |
| ソースフィールド | - | string | 暗号化/復号するフィールド名を入力します。 | |
| 保存するフィールド | - | string | 暗号化/復号の結果を保存するフィールド名を入力します。 | |
| 上書き | `false` | boolean | 指定した対象フィールド名に値が存在する場合、これを上書きするかどうかを選択します。 | |

### encryptの例

#### 条件

* mode → `encrypt`
* Appkey → `SKM Appkey`
* キーID → `SKM 共通鍵ID`
* キーバージョン → `1`
* ソースフィールド → message
* 保存するフィールド → encrypted_message

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

### decryptの例

#### 条件

* mode → `decrypt`
* Appkey → `SKM Appkey`
* キーID → `SKM 共通鍵ID`
* キーバージョン → `1`
* ソースフィールド → message
* 保存するフィールド → decrypted_message

#### 入力メッセージ

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

#### 出力メッセージ

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
    "decrypted_message": "this is plain message",
}
```

## Filter > CSV

### ノードの説明

* CSV形式のメッセージをパースしてフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|-----------|--------------------------|------------------|------------------------------------------------|--------------------------|
| 保存するフィールド | - | string | CSVパース結果を保存するフィールド名を入力します。 | |
| Quote | `"` | string | カラムフィールドを区切る文字を入力します。 | |
| 最初の行を無視するかどうか | `false` | boolean | プロパティ値がtrueの場合、読み込んだデータのうち最初の行に入力されたカラム名を無視します。 | |
| 区切り文字 | , | string | カラムを区切る文字列を入力します。 | |
| ソースフィールド | - | string | CSVパースするフィールド名を入力します。 | |
| スキーマ | - | hash | 各カラムの名前とデータ型をdictionary形式で入力します。 | `スキーマ入力方法`を参照 |
| 上書き | `false` | boolean | trueの場合、CSVパース結果が保存するフィールドや既存のフィールドと重複すれば上書きします。 | |
| 元のフィールドを削除 | `false` | boolean | CSVパースが完了するとソースフィールドを削除します。パースに失敗した場合は維持します。 | |

#### スキーマ入力方法
* カラムタイプはサポートしておらず、全てのカラム及びデータ型をスキーマとして入力として受け取ります。
* 使用可能なデータ型は次のとおりです。
    * string, integer, long, float, double, boolean


### データ型がないCSVパースの例

#### 条件

* ソースフィールド → `message`
* スキーマ → `{"one": "string", "two": "string", "t hree": "string"}`

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


### データ型変換が必要なCSVパースの例

#### 条件

* ソースフィールド → `message`
* スキーマ → `{"one": "string", "two": "integer", "t hree": "boolean"}`

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

* JSON文字列をパースして指定されたフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド | - | string | JSON文字列をパースするフィールド名を入力します。 | |
| 保存するフィールド | - | string | JSONパース結果を保存するフィールド名を入力します。<br/>もしプロパティ値を指定しない場合、rootフィールドに結果を保存します。 | |
| 上書き | `false` | boolean | trueの場合、JSONパース結果が保存するフィールドや既存のフィールドと重複すれば上書きします。 | |
| 元のフィールドを削除 | `false` | boolean | JSONパースが完了するとソースフィールドを削除します。パースに失敗した場合は維持します。 | |

### JSONパースの例

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
    "message": "{\\\"json\\\": \\\"parse\\\", \\\"example\\\": \\\"string\\\"}"
}
```

## Filter > Date

### ノードの説明

* Date文字列をパースしてtimestamp形式で保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|--------|----------------------------------|------------------|--------------------------------------|-----------------------------------------------------------------|
| ソースフィールド | - | string | 文字列を取得するためのフィールド名を入力します。 | |
| 形式 | - | array of strings | 文字列を取得するための形式を入力します。 | 事前定義された形式は次のとおりです。<br/>ISO8601、UNIX、UNIX_MS |
| Locale | `ko_KR` | string | Date文字列の分析に使用するLocaleを入力します。 | 例: en、en-US、ko_KR |
| 保存するフィールド | - | string | Date文字列パース結果を保存するフィールド名を入力します。 | |
| タイムゾーン | `Asia/Seoul` | string | 日付のタイムゾーンを入力します。 | 例: Asia/Seoul |

### Date文字列パースの例

#### 条件

* ソースフィールド → `message`
* 形式 → `["yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
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

* UUIDを生成してフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| UUID保存フィールド | - | string | UUID生成結果の値を保存するフィールド名を入力します。 | |
| 上書き | `false` | boolean | 指定されたフィールド名に値が存在する場合、これを上書きするかどうかを選択します。 | |

### UUID生成の例

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

## Filter > Convert

### ノードの説明

* 特定のフィールドのデータ型を変換するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|-------|-----|--------|-----------------------------------------------------------------------------|----|
| 対象フィールド | - | string | データ型を変換する対象フィールドを入力します。 | |
| 変換タイプ | - | enum | 変換するデータ型を選択します。<br/> * 提供タイプ: `STRING, INTEGER, FLOAT, DOUBLE, BOOLEAN` | |

### データ変換の例

#### 条件

* 対象フィールド → `message`
* 変換タイプ → `INTEGER`

#### 入力メッセージ

```js
{
    "message": "2025"
}
```

#### 出力メッセージ

```js
{
    "message": 2025
}
```


## Filter > Coerce

### ノードの説明

* null値をデフォルト値に置き換えるノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 対象フィールド | - | string | デフォルト値を指定するフィールド名を入力します。 | |
| デフォルト値 | - | string | デフォルト値を入力します。 | |

### デフォルト値の設定例

#### 条件
* 対象フィールド → `fieldname`
* デフォルト値 → `default_value`

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

## Filter > Copy

### ノードの説明

* 既存のフィールドを他のフィールドにコピーするノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 対象フィールド | - | string | コピーするソースフィールド名を入力します。 | |
| 保存するフィールド | - | string | コピーした結果を保存するフィールド名を入力します。 | |
| 上書き | `false` | boolean | trueの場合、保存するフィールドがすでに存在すれば上書きします。 | |

### デフォルト値の設定例

#### 条件
* 対象フィールド → `source_field`
* 保存するフィールド → `dest_field`

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

## Filter > Rename

### ノードの説明

* フィールド名を変更するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド | | string | 名前を変更するソースフィールドを入力します。 | |
| 対象フィールド | - | string | 変更するフィールド名を入力します。 | |
| 上書き | `false` | boolean | trueの場合、対象フィールドがすでに存在すれば上書きします。 | |

### デフォルト値の設定例

#### 条件
* ソースフィールド → `fieldname`
* 対象フィールド → `changed_fieldname`

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

## Filter > Strip

### ノードの説明

* フィールドの文字列の前後の空白を削除するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 対象フィールド | - | array of strings | 空白を削除する対象フィールドを入力します。 | |

### デフォルト値の設定例

#### 条件
* 対象フィールド → `["field1", "field2"]`

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

## Filter > Remove Fields

### ノードの説明

* フィールドを削除するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|--------|-----|------------------|--------------------|----|
| 削除するフィールド | - | array of strings | 削除するフィールド名の一覧を入力します。 | |

### 設定例

#### 条件
* 削除するフィールド → `["field2", "field3"]`

#### 入力メッセージ

```json
{
    "field1": "value1",
    "field2": "value2",
    "field3": "value3",
    "field4": "value4"
}
```

#### 出力メッセージ

```json
{
    "field1": "value1",
    "field4": "value4"
}
```

## Sink

* Filter処理を終えたデータを保存するエンドポイントを定義するノードタイプです。

### Sinkノードの共通設定

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値でチャートボードにノード名を表示します。 |  |

## Sink > (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageにデータをアップロードするノードです。
* 他の設定を行わずに基本設定のみで作成すると、オブジェクトは次のパスフォーマットに合わせて出力されます。
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* 提供されるコーデックはjson、line、parquetです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|-----------------------|------------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| リージョン | - | enum | Object Storage商品のリージョンを入力します。 | |
| バケット | - | string | バケット名を入力します。 | |
| シークレットキー | - | string | S3 API資格情報のシークレットキーを入力します。 | |
| アクセスキー | - | string | S3 API資格情報のアクセスキーを入力します。 | |
| Prefix | `/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}` | string | オブジェクトのアップロード時に名前の前に付けるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | - | string | Prefixに適用する時間フィールドを入力します。 | |
| Prefix時間フィールドタイプ | `DATE_FILTER_RESULT` | enum | Prefixに適用する時間フィールドのタイプを入力します。 | DATE_FILTER_RESULTタイプのみ可能(今後他のタイプもサポート予定) |
| Prefixタイムゾーン | `UTC` | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 | |
| Prefix時間適用fallback | `_prefix_datetime_parse_failure` | string | Prefix時間の適用に失敗した場合に代替するPrefixを入力します。 | |
| 基準時刻 | `1` | number | オブジェクトを分割する基準となる時間を設定します。 | |
| 基準オブジェクトサイズ | `5242880` | number | オブジェクトを分割する基準となるサイズ(単位： byte)を設定します。 | |
| 非アクティブ間隔 | `1` | number | データの取り込みがない状態が継続した場合にオブジェクトを分割する基準時間を設定します。 | 設定された時間内にデータの取り込みがなければ現在のオブジェクトがアップロードされ、以降に新たに取り込まれるデータは新しいオブジェクトに作成されます。 |

### コーデック別の出力例

**サポートするコーデック:**
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース
* [lineコーデック](./codec-config-guide.md#line) - 行単位のメッセージ処理
* [parquetコーデック](./codec-config-guide.md#parquet) - データをparquet形式で圧縮 

### Prefixの例 - フィールド

#### 条件

* バケット → `obs-test-container`
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
/obs-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefixの例 - 時間

#### 条件

* バケット → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド → `logTime`
* Prefix時間フィールドタイプ → `ISO8601`
* Prefixタイムゾーン → `Asia/Seoul`

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
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefixの例 - 時間の適用に失敗した場合

#### 条件

* バケット → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド → `logTime`
* Prefix時間フィールドタイプ → `TIMESTAMP_SEC`
* Prefixタイムゾーン → `Asia/Seoul`
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
/obs-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (Amazon) S3

### ノードの説明

* Amazon S3にデータをアップロードするノードです。
* 提供されるコーデックはjson、line、parquetです。

### プロパティの説明
| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| リージョン | - | enum | S3商品のリージョンを入力します。 | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| バケット | - | string | バケット名を入力します。 | |
| アクセスキー | - | string | S3 API資格情報のアクセスキーを入力します。 | |
| シークレットキー | - | string | S3 API資格情報のシークレットキーを入力します。 | |
| Prefix | - | string | オブジェクトのアップロード時に名前の前に付けるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | - | string | Prefixに適用する時間フィールドを入力します。 | |
| Prefix時間フィールドタイプ | `DATE_FILTER_RESULT` | enum | Prefixに適用する時間フィールドのタイプを入力します。 | DATE_FILTER_RESULTタイプのみ可能(今後他のタイプもサポート予定) |
| Prefixタイムゾーン | `UTC` | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 | |
| Prefix時間適用fallback | `_prefix_datetime_parse_failure` | string | Prefix時間の適用に失敗した場合に代替するPrefixを入力します。 | |
| 基準時刻 | `1` | number | オブジェクトを分割する基準となる時間を設定します。 | |
| 基準オブジェクトサイズ | `5242880` | number | オブジェクトを分割する基準となるサイズを設定します。 | |
| パス方式リクエスト | `false` | boolean | パス方式リクエストを使用するかどうかを決定します。 | |
| 非アクティブ間隔 | `1` | number | データの取り込みがない状態が継続した場合にオブジェクトを分割する基準時間を設定します。 | 設定された時間内にデータの取り込みがなければ現在のオブジェクトがアップロードされ、以降に新たに取り込まれるデータは新しいオブジェクトに作成されます。 |

!!! danger "注意"
    * (Amazon) S3ノードを使用してNHN Cloud Object Storageに接続する場合、**パス方式リクエスト**を`true`に設定する必要があります。


### コーデック別の出力例

**サポートするコーデック:**
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データのパース
* [lineコーデック](./codec-config-guide.md#line) - 行単位のメッセージ処理
* [parquetコーデック](./codec-config-guide.md#parquet) - データをparquet形式で圧縮 

## Sink > (Apache) Kafka

### ノードの説明

* Kafkaにデータを送信するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|-------------|--------------|--------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| トピック | - | string | メッセージを送信するKafkaトピック名を入力します。 | |
| ブローカーサーバー一覧 | | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はカンマ(`,`)で区切ります。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`bootstrap.servers`プロパティを参照<br/>例: 10.100.1.1:9092,10.100.1.2:9092 |
| クライアントID | `dataflow` | string | Kafka Producerを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`client.id`プロパティを参照 |
| 圧縮タイプ | `none` | enum | 送信するデータを圧縮する方法を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/topic-level-configs/)の`compression.type`プロパティを参照<br/>none、gzip、snappy、lz4、zstdの中から選択 |
| メッセージキー | - | string | メッセージキーとして使用するフィールドを入力します。 | |
| メタデータ更新周期 | `300000` | number | パーティション、ブローカーサーバーの状態などを更新する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`metadata.max.age.ms`プロパティを参照 |
| 最大リクエストサイズ | `1048576` | number | 送信リクエストごとの最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`max.request.size`プロパティを参照 |
| サーバー再接続周期 | `50` | number | ブローカーサーバーへの接続に失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`reconnect.backoff.ms`プロパティを参照 |
| バッチサイズ | `16384` | number | バッチリクエストで送信するサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`batch.size`プロパティを参照 |
| バッファーメモリ | `33554432` | number | Kafkaの送信に使用するバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`buffer.memory`プロパティを参照 |
| 受信バッファーサイズ | `32768` | number | データの読み込みに使用するTCP receiveバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`receive.buffer.bytes`プロパティを参照 |
| 送信遅延時間 | `0` | number | メッセージ送信を遅延させる時間を入力します。遅延されたメッセージはバッチリクエストで一度に送信します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`linger.ms`プロパティを参照 |
| サーバーリクエストタイムアウト | `30000` | number | 送信リクエストに対するタイムアウト(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`request.timeout.ms`プロパティを参照 |
| 送信バッファーサイズ | `131072` | number | データを送信するために使用するTCP sendバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`send.buffer.bytes`プロパティを参照 |
| ackプロパティ | `all` | enum | ブローカーサーバーでメッセージを受信したかを確認する設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`acks`プロパティを参照<br/>0 - メッセージの受信の有無を確認しません。<br/>1 - トピックのleaderが、followerがデータをコピーするのを待たずにメッセージを受信したと応答します。<br/>all - トピックのleaderが、followerがデータをコピーするのを待った後、メッセージを受信したと応答します。 |
| 再試行リクエスト周期 | `100` | number | 送信リクエストが失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`retry.backoff.ms`プロパティを参照 |
| 再試行回数 | `2147483647` | number | 送信リクエストが失敗した場合に再試行する最大回数を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`retries`プロパティを参照<br/>設定値を超過して再試行する場合、データの消失が発生する可能性があります。 |
| 追加設定 | - | hash | Kafka接続に使用する追加のProducer設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)を参照 |

### コーデック別の出力例

**サポートするコーデック:**
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データの出力
* [lineコーデック](./codec-config-guide.md#line) - 行単位のメッセージ出力

## Sink > Stdout

### ノードの説明

* 標準出力にメッセージを出力するノードです。
* Source、Filterノードで処理されたデータを確認する際に役立ちます。

### コーデック別の出力例

**サポートするコーデック:**
* [jsonコーデック](./codec-config-guide.md#json) - JSON形式データの出力
* [lineコーデック](./codec-config-guide.md#line) - 行単位のメッセージ出力

## Branch

* 取り込まれたデータの値に応じてフローの分岐を定義するノードタイプです。

## IF

### ノードの説明

* 条件文を通じてメッセージをフィルタリングするノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 条件文 | - | string | メッセージをフィルタリングする条件を入力します。 | 以下の例を参考にしてください。 |

#### 使用可能な演算子
* 比較: ==、!=、<、>、<=、>=
* 正規表現: =~ (右辺に与えられたパターンで左辺の文字列を検査)
* 包含: =~、!~、.contains()
* 論理演算子: &&、||、not
* 否定演算子: !、not

### フィルタリングの例 - first depth field reference

#### 条件
* 条件文 → `logLevel == "ERROR"`

#### 通過メッセージ

``` json
{
    "logLevel": "ERROR"
}
```

#### 除外メッセージ

``` json
{
    "logLevel": "INFO"
}
```

### フィルタリングの例 - second depth field reference

#### 条件

* 条件文 → `response.status == 200`または`response["status"] == 200`

#### 通過メッセージ

``` json
{
    "response": {
        "status": 200
    }
}
```

#### 除外メッセージ

``` json
{
    "response": {
        "status": 404
    }
}
```
