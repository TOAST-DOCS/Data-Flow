## Data & Analytics > DataFlow > リリースノート

### 2023. 07. 25.

#### バグ修正

* フロー実行中に失敗して再実行する際、最後に実行したポイントから引き続き実行できるように修正しました。

### 2023. 06. 27.
#### 機能追加
* Log & Crash Search連動機能の追加
  * フローのログをLog & Crash Searchに保存できる機能を追加しました。
#### バグ修正
* ログ表示ボタンの有効化タイミングを調整
  * ログ表示ボタンの有効化タイミングをPREPARING段階に修正しました。

### 2023. 03. 28.

#### 機能追加

* 新規ノード追加
    * Filter
        * Alter、Date、UUID、Split、Truncateなどさまざまなデータ処理方法を追加しました。
* フロー使用量表示機能を追加
    * コンソールでフロー使用量をリアルタイムで確認できる機能を追加しました。

#### バグ修正

* PREPARING段階に入る前にフロー使用量が先に上がるバグを修正しました。

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
