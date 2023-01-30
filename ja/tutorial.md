## Data & Analytics > DataFlow > チュートリアル

### フローの作成

![chapter1.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter1.png)

① **フローの作成**をクリックします。
② **フロー名**を入力します。
③ **フローの説明**を入力します。
④ **フローテンプレート**で**LNCS to OBS**を選択します。

### Log & Crash Searchノードの定義

![chapter2.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter2.png)

上記の項で作成したフローを選択し、以下のように設定します。

① **フロー情報**タブをクリックし、**(NHN Cloud) Log&Crash Search_1**ノードをクリックします。
②データソースに指定する(NHN Cloud) Log&Crash Searchの**アプリケーションキー**を入力します。 [アプリケーションキーの抽出](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/console-guide/#appkey)

### Cipherノードの定義

![chapter3.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter3.png)

Cipherノードを定義するには**Cipher_2**ノードをクリックし、次のように設定します。

① **キーバージョン**は、使用するSecurity Key Manager(SKM)キーストアの対称鍵バージョンを入力します。[キーバージョンを確認する](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/console-guide/)
② **アプリケーションキー**は、SKMのアプリケーションキーを入力します。
③ **キーID**は、SKMキーストアの対称鍵IDを入力します。

### Object Storageノードの定義とフローの保存

![chapter4.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter4.png)

Object Storageノードを定義するには**Object Storage_3**ノードをクリックし、次のように設定します。

① **バケット**にはデータを保存するバケットを入力します。
② **アクセスキー**にはS3 API認証情報アクセスキーを入力します。[アクセスキーの発行](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
③ **秘密鍵**にはS3 API認証情報秘密鍵を入力します。[秘密鍵の発行](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
④ **フローの保存**をクリックしてフローを保存します。

### フローの実行

![chapter5.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter5.png)

①フローを選択します。
②さらに表示アイコンをクリックしてメニューを開きます。
③ **フローの開始**をクリックしてフローを実行します。

### 実行後の作業

![chapter6.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter6.png)

①フローを開始し、1～2分経過後に**更新**をクリックします。
② **実行状態**が緑色に変わります。
③ **ログを表示**をクリックしてフロー実行の詳細ログを確認できます。
