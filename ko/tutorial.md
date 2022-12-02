### 플로우 생성

![chapter1.png](http://static.toastoven.net/prod_dataflow/tutorial/chapter1.png)

1. 플로우 생성 버튼을 클릭합니다.
2. 플로우 이름을 입력합니다.
3. 플로우 설명을 입력합니다.
4. 플로우 템플릿에서 LNCS to OBS를 선택합니다.

### Log & Crash Search 노드 정의

![chapter2.png](http://static.toastoven.net/prod_dataflow/tutorial/chapter2.png)

0. 위에서 생성된 플로우를 클릭합니다.
1. 플로우 정보 탭을 클릭합니다. 그리고 (NHN Cloud) Log&Crash Search\_1 노드를 클릭합니다.
2. 데이터 소스로 지정하고자 하는 (NHN Cloud) Log&Crash Search의 Appkey를 입력합니다. [Appkey 추출](https://docs.toast.com/ko/Data%20&%20Analytics/Log%20&%20Crash%20Search/ko/console-guide/#appkey)

### Cipher 노드 정의

![chapter3.png](http://static.toastoven.net/prod_dataflow/tutorial/chapter3.png)

0. Cipher\_2 node를 클릭합니다.
1. 키 버전은 사용하고자 하는 Security Key Manager(SKM) 키 저장소의 대칭키 버전을 입력합니다. [키 버전 확인하기](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/console-guide/)
2. 앱키는 SKM의 앱키를 입력합니다.
3. 키 ID는 SKM 키 저장소의 대칭키 ID를 입력합니다.

### Object Storage 노드 정의와 플로우 저장

![chapter4.png](http://static.toastoven.net/prod_dataflow/tutorial/chapter4.png)

0. (NHN Cloud) Object Storage_3 노드를 클릭합니다.
1. 데이터를 저장할 버킷을 입력합니다.
2. 액세스 키에는 S3 API 자격 증명 액세스 키를 입력합니다. [액세스 키 발급](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
3. 비밀키에는 S3 API 자격 증명 비밀키를 입력합니다. [비밀키 발급](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
4. 그리고 플로우를 저장합니다.

### 플로우 실행

![chapter5.png](http://static.toastoven.net/prod_dataflow/tutorial/chapter5.png)

1. 플로우를 시작하고 1\~2분 경과 뒤에 새로고침을 클릭합니다.
2. 실행 상태가 초록불이 되는 것을 확인할 수 있습니다.
3. 로그보기를 클릭해서 플로우 실행에 관한 상세 로그를 확인할 수 있습니다.