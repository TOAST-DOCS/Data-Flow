## Data & Analytics > DataFlow > 튜토리얼

### 플로우 생성

![chapter1.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter1_2025_08.png)

① **플로우 생성** 버튼을 클릭합니다.
② **플로우 이름**을 입력합니다.
③ **플로우 설명**을 입력합니다.
④ **실행 모드**를 선택합니다.
   - **STREAMING**: 플로우 템플릿 선택으로 인해 자동 설정됩니다.
⑤ **플로우 템플릿**에서 **LNCS to OBS**를 선택합니다.
   - **LNCS to OBS** 템플릿은 `NHN Cloud Log & Crash Search`에서 데이터를 조회 및 변환 과정을 거쳐 `Object Storage`에 저장하는 플로우입니다.
⑥ **인스턴스 타입**을 선택합니다.

### Log & Crash Search 노드 정의

![chapter2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2_2025_08.png)

① 생성한 플로우의 **플로우 정보** 탭을 클릭합니다.
② **(NHN Cloud) Log&Crash Search** 노드를 클릭합니다.
③ 데이터 소스로 지정할 (NHN Cloud) Log&Crash Search의 **Appkey**와 **Secretkey**를 입력합니다.

### filter success response 노드 정의

![chapter2-2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2-2_2025_08.png)

① **filter success response** 노드를 클릭합니다.
② **LNCS to OBS** 템플릿에서는 Log&Crash Search Source 노드의 데이터 조회 결과가 정상인 경우에만 IF 노드를 통과하도록 조건문이 작성되어 있습니다.
> 만일 `True`를 `False`로 변경하는 경우, Log&Crash Search Source 노드의 데이터 조회 결과가 정상이 아닌 경우에 IF 노드를 통과하게 됩니다.

### Cipher 노드 정의

![chapter3.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter3_2025_08.png)

① **Cipher** 노드를 클릭합니다.
② SKM 사용을 위해 아래 정보를 입력합니다.
 - **키 버전**: 사용할 Security Key Manager(SKM) 키 저장소의 대칭 키 버전
 - **앱키**: SKM의 앱키
 - **키 ID**: SKM 키 저장소의 대칭 키 ID 

!!! tip "알아두기"
    대칭 키 버전은 Secure Key Manager 웹 콘솔의 키 상세 정보에서 확인할 수 있습니다.

### Object Storage 노드 정의 및 플로우 저장

![chapter4.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter4_2025_08.png)

① **(NHN Cloud) Object Storage**노드를 클릭합니다.
② 데이터를 저장할 버킷 이름을 입력합니다.
③ 버킷 접근을 위한 자격 증명 정보를 입력합니다.
  - **액세스 키**: S3 API 자격 증명 액세스 키
  - **비밀 키**: S3 API 자격 증명 비밀 키
④ **플로우 저장** 버튼을 통해 플로우를 저장합니다.

!!! tip "알아두기"
    S3 API 자격 증명 액세스 키 및 비밀 키는 Object Storage 웹 콘솔 또는 Object Storage의 S3 API 자격 증명 발급 API를 통해 발급할 수 있습니다.

### 플로우 실행

![chapter5.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter5_2025_08.png)

① 실행할 플로우를 선택합니다.
② 더 보기 아이콘 > **플로우 시작** 버튼을 통해 플로우를 실행합니다.

### 실행 이후 작업

![chapter6.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter6_2025_08.png)

① 플로우를 시작하면 1~2분 후에 실행 상태가 초록색으로 변경됩니다.
② **로그 보기** 버튼을 클릭해 플로우 실행에 대한 상세 로그를 확인할 수 있습니다.