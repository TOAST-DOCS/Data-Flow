## Data & Analytics > DataFlow > Tutorial

### Create Flow 

![chapter1.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter1_v2.png)

① Click **Create Flow**.
② Enter **Flow Name**.
③ Enter **Flow Description**.
④ Select **LNCS to OBS** from **Flow Template**. 

### Log & Crash Search Node Definition 

![chapter2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2_v2.png)

Click the flow created above and configure the settings as follows.

① Click the **Flow Information** tab and click the **(NHN Cloud) Log&Crash Search**.
② Enter the **Appkey** and **SecretKey** for (NHN Cloud) Log & Crash Search you want to specify as Data source. [Extract Appkey](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/console-guide/#appkey)

### Filter Success Response Node

![chapter2-2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2-2_v2.png)

(NHN Cloud) You can filter the data input to the Log&Crash Search Source node with an IF node.

① In the **LNCS to OBS** template, a conditional statement is written to pass the IF node only if the data search result of the Log & Crash Search Source node is normal.
② If you change True to False, the data search result of the Log&Crash Search Source node will pass the IF node when the search result is `not normal`.

### Cipher Node Definition

![chapter3.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter3_v2.png)

To define the Cipher node, click **Cipher** and configure the settings as follows.

① For **Key Version**, enter the symmetric key version of the Security Key Manager (SKM) key store that you want to use. [Check Key Version](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/console-guide/)
② For **AppKey**, enter the appkey of SKM.
③ For **Key ID**, entere the symmetric key ID of SKM key store.

### Define Object Storage Node and Save Flow 

![chapter4.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter4_v2.png)

To define the Object Storage noe, click **Object Storage** and configure the settings as follows.

① For **Bucket**, enter a bucket where you want to store data.
② For **Access Key**, enter the S3 API Credential access key. [Access Key Issuance](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
③ For **Secret Key**, enter the S3 API Credential secret key. [Secret key Issuance](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
④ Click **Save Flow** to save the flow.

### Flow Execution

![chapter5.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter5_v2.png)

① Start Flow and click Refresh after 1 to 2 minutes.
② Can check that execution status turns to green light.
③ Click View Logs to view detailed Logs about Flow execution.

### Job After Execution

![chapter6.png](http://static.toastoven.net/prod_dataflow/en/tutorial/chapter6_v2.png)

① When a minute or two have passed after starting the flow, click **Refresh**.
② **Execution Status** changes to green.
③ Click **View Logs** to check the detailed logs on flow execution.