## Data & Analytics > DataFlow > 튜토리얼

### 플로우 생성

![chapter1.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter1_v2.png)

① **플로우 생성**을 클릭합니다.
② **플로우 이름**을 입력합니다.
③ **플로우 설명**을 입력합니다.
④ **플로우 템플릿**에서 **LNCS to OBS**를 선택합니다.

### Log & Crash Search 노드 정의

![chapter2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2_v2.png)

위 항에서 생성한 플로우를 선택한 뒤 아래와 같이 설정합니다.

① **플로우 정보** 탭을 클릭한 뒤 **(NHN Cloud) Log&Crash Search** 노드를 클릭합니다.
② 데이터 소스로 지정할 (NHN Cloud) Log&Crash Search의 **Appkey**와 **Secretkey**를 입력합니다.

### filter success response 노드

![chapter2-2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2-2_v2.png)

(NHN Cloud) Log&Crash Search Source 노드로 인입된 데이터를 IF 노드로 필터 처리할 수 있습니다.

① **LNCS to OBS** 템플릿에서는 Log&Crash Search Source 노드의 데이터 조회 결과가 정상인 경우에만 IF 노드를 통과하도록 조건문이 작성되어 있습니다.
② 만약 True를 False로 바꿀 경우, Log&Crash Search Source 노드의 데이터 조회 결과가 `정상이 아닌 경우`에 IF 노드를 통과하게 됩니다.

### Cipher 노드 정의

![chapter3.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter3_v2.png)

Cipher 노드를 정의하려면 **Cipher** 노드를 클릭한 뒤 아래와 같이 설정합니다.

① **키 버전**은 사용할 Security Key Manager(SKM) 키 저장소의 대칭 키 버전을 입력합니다. [키 버전 확인하기](https://docs.toast.com/ko/Security/Secure%20Key%20Manager/ko/console-guide/)
② **앱키**는 SKM의 앱키를 입력합니다.
③ **키 ID**는 SKM 키 저장소의 대칭 키 ID를 입력합니다.

### Object Storage 노드 정의와 플로우 저장

![chapter4.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter4_v2.png)

Object Storage 노드를 정의하려면 **Object Storage** 노드를 클릭한 뒤 아래와 같이 설정합니다.

① **버킷**에는 데이터를 저장할 버킷을 입력합니다.
② **액세스 키**에는 S3 API 자격 증명 액세스 키를 입력합니다. [액세스 키 발급](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
③ **비밀 키**에는 S3 API 자격 증명 비밀 키를 입력합니다. [비밀 키 발급](https://docs.toast.com/ko/Storage/Object%20Storage/ko/s3-api-guide/#s3-api)
④ **플로우 저장**을 클릭해 플로우를 저장합니다.

### 플로우 실행

![chapter5.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter5_v2.png)

① 플로우를 선택합니다.
② 더 보기 아이콘을 클릭해 메뉴를 펼칩니다.
③ **플로우 시작**을 클릭해 플로우를 실행합니다.

### 실행 이후 작업

![chapter6.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter6_v2.png)

① 플로우를 시작하고 1~2분 경과 뒤에 **새로 고침**을 클릭합니다.
② **실행 상태**가 초록색으로 변경됩니다.
③ **로그보기**를 클릭해 플로우 실행에 대한 상세 로그를 확인할 수 있습니다.