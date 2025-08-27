## Data & Analytics > DataFlow > チュートリアル

### フローの作成

![chapter1.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter1_2025_08.png)

① **フローの作成**ボタンをクリックします。
② **フロー名**を入力します。
③ **フローの説明**を入力します。
④ **実行モード**を選択します。
   - **STREAMING**: フローテンプレートの選択により、自動で設定されます。
⑤ **フローテンプレート**で**LNCS to OBS**を選択します。
   - **LNCS to OBS**テンプレートは、`NHN Cloud Log & Crash Search`からデータを照会・変換し、`Object Storage`に保存するフローです。
⑥ **インスタンスタイプ**を選択します。

### Log & Crash Searchノードの定義

![chapter2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2_2025_08.png)

① 作成したフローの**フロー情報**タブをクリックします。
② **(NHN Cloud) Log&Crash Search**ノードをクリックします。
③ データソースとして指定する(NHN Cloud) Log&Crash Searchの**AppKey**と**SecretKey**を入力します。

### filter success responseノードの定義

![chapter2-2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2-2_2025_08.png)

① **filter success response**ノードをクリックします。
② **LNCS to OBS**テンプレートでは、Log&Crash Search Sourceノードのデータ照会結果が正常な場合にのみIFノードを通過するよう、条件文が作成されています。
> もし`True`を`False`に変更した場合、Log&Crash Search Sourceノードのデータ照会結果が正常でない場合にIFノードを通過することになります。

### Cipherノードの定義

![chapter3.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter3_2025_08.png)

① **Cipher**ノードをクリックします。
② SKMを使用するため、以下の情報を入力します。
 - **キーバージョン**:使用するSecurity Key Manager(SKM)キーストアの対称鍵バージョン
 - **アプリキー**: SKMのアプリキー
 - **キーID**: SKMキーストアの対称鍵ID 

!!! tip "「知っておくべきこと」"
   対称鍵のバージョンはSecure Key Manager Webコンソールのキー詳細情報で確認できます。
    
### Object Storageノードの定義とフローの保存

![chapter4.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter4_2025_08.png)

① **(NHN Cloud) Object Storage**ノードをクリックします。
② データを保存するバケット名を入力します。
③ バケットにアクセスするための認証情報を入力します。
  - **アクセスキー**: S3 API認証情報のアクセスキー
  - **シークレットキー**: S3 API認証情報のシークレットキー
④ **フロー保存**ボタンでフローを保存します。

!!! tip "「知っておくべきこと」"
    S3 API認証情報アクセスキー及び秘密鍵はObject StorageのWebコンソールまたはObject StorageのS3 API認証情報発行APIを利用して発行できます。
    
### フローの実行

![chapter5.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter5_2025_08.png)

①実行するフローを選択します。
② その他アイコン > **フロー開始**ボタンでフローを実行します。

### 実行後の作業

![chapter6.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter6_2025_08.png)

① フローを開始して1〜2分後、実行ステータスが緑色に変わります。
② **ログ表示**ボタンをクリックし、フロー実行に関する詳細ログを確認できます。
