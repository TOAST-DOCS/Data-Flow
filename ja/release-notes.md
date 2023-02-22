## Data & Analytics > DataFlow > リリースノート

### 2023. 02. 28.

#### 機能追加

* 新規ノード追加
    * Source
        * NHN Cloud ObjectStorageノードからデータを取得する機能を追加しました。
        * Amazon S3インタフェースを介してデータを取得する機能を追加しました。
        * Apache Kafkaを介してデータを取得する機能を追加しました。
    * Filter
        * grok、json、csvノードを追加してデータをさまざまに前処理する機能を追加しました。

### 2023. 01. 06.

#### バグ修正

* フロー編集画面でノードを追加した後に最初のボタンクリックが動作しない問題を修正しました。
* Cipherノードのフィールド追加を設定しても実際にはフィールドが追加されていない問題を修正しました。

### 2022. 12. 27.

#### 新規サービスリリース

* ETLフローの作成および実行ができるサービスです。
* 下記のSourceをサポートします。
    * NHN Cloud Log & Crash Search
    * NHN Cloud CloudTrail
* 下記のFilterをサポートします。
    * Cipher (NHN Cloud Secure Key Manager連動必要)
* 下記のSinkをサポートします。
    * NHN Cloud Object Storage
    * Amazon S3 (Compatible)
    * Apache Kafka
