## Data & Analytics > DataFlow > チュートリアル

### フローの作成

![chapter1.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter1_v2.png)

① **フローの作成**をクリックします。
② **フロー名**を入力します。
③ **フローの説明**を入力します。
④ **フローテンプレート**で**LNCS to OBS**を選択します。

### Log & Crash Searchノードの定義

![chapter2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2_v2.png)

上記の項で作成したフローを選択し、以下のように設定します。

① **フロー情報**タブをクリックし、**(NHN Cloud) Log&Crash Search**ノードをクリックします。
②データソースに指定する(NHN Cloud) Log&Crash Searchの**Appkey**と **Secretkey**を入力します。

### filter success responseノード

![chapter2-2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2-2_v2.png)

(NHN Cloud) Log&Crash Search Sourceノードに入力されたデータをIFノードでフィルタ処理できます。

① **LNCS to OBS** テンプレートでは、Log&Crash Search Sourceノードのデータ照会結果が正常な場合のみIFノードを通過するように条件文が作成されています。
② もしTrueをFalseに変更する場合、Log&Crash Search Sourceノードのデータ照会結果が「正常でない場合」にIFノードを通過することになります。

### Cipherノードの定義

![chapter3.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter3_v2.png)

Cipherノードを定義するには**Cipher**ノードをクリックし、次のように設定します。

① **キーバージョン**は、使用するSecurity Key Manager(SKM)キーストアの対称鍵バージョンを入力します。
② **アプリケーションキー**は、SKMのアプリケーションキーを入力します。
③ **キーID**は、SKMキーストアの対称鍵IDを入力します。

!!! tip "「知っておくべきこと」"
    対称鍵のバージョンはSecure Key Manager Webコンソールのキー詳細情報で確認できます。
    
### Object Storageノードの定義とフローの保存

![chapter4.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter4_v2.png)

Object Storageノードを定義するには**Object Storage**ノードをクリックし、次のように設定します。

① **バケット**にはデータを保存するバケットを入力します。
② **アクセスキー**にはS3 API認証情報アクセスキーを入力します。
③ **秘密鍵**にはS3 API認証情報秘密鍵を入力します。
④ **フローの保存**をクリックしてフローを保存します。

!!! tip "「知っておくべきこと」"
    S3 API認証情報アクセスキー及び秘密鍵はObject StorageのWebコンソールまたはObject StorageのS3 API認証情報発行APIを利用して発行できます。
    
### フローの実行

![chapter5.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter5_v2.png)

①フローを選択します。
②さらに表示アイコンをクリックしてメニューを開きます。
③ **フローの開始**をクリックしてフローを実行します。

### 実行後の作業

![chapter6.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter6_v2.png)

①フローを開始し、1～2分経過後に**更新**をクリックします。
② **実行状態**が緑色に変わります。
③ **ログを表示**をクリックしてフロー実行の詳細ログを確認できます。
