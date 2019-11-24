# 개발자 문서에 대한 VAS 인증 액세스

​	모든 개발자에게 경의를 표하며 전 세계를 변화시키고 있습니다!

사용자는 타사 웹 페이지 또는 APP에서 VAS 인증 메커니즘을 통해 기본 사용자 정보를 얻고 사용자 지불을받을 수 있으므로 비즈니스 로직을 구현하고 VAS 에코 시스템을 만들 수 있습니다.

VAS 지갑의 스커트는 vaswallet입니다

## 승인 된 액세스 신청

​		액세스 당사자는 vas wallet 인증 애플리케이션에 관련 타사 애플리케이션 정보를 입력해야하며 커뮤니티는 검토를 통과 한 후 APP의 액세스 상황을 피드백합니다.

> 승인 된 콜백 도메인 이름 구성 사양은 전체 도메인 이름입니다. 예를 들어, 웹 페이지 인증에 필요한 도메인 이름은 www.vasblock.com이고 도메인 이름의 다음 페이지는 http://www.vasblock.com/music.html, 로 구성됩니다. 승인은  http://www.vasblock.com/login.html 에서 수행 할 수 있습니다. 그러나 http://pay.vasblock.com, http://music.vasblock.com 및 http://vasblock.com은 권한 부여 작업을 수행 할 수 없습니다.
>
> 휴대 전화 H5에서 시작한 인증 작업 인 경우 체계 입력에서 http 또는 https를 입력하고 보안 콜백 도메인 이름을 입력하십시오.

​		액세스 응용 프로그램이 승인되면 uuid, accessToken, rsaPrivate, rsaPublic을 얻습니다 (DAPP 액세스 응용 프로그램 레코드에서 "세부 정보보기"를 클릭하십시오. 궁금한 사항이 있으면 vas.github@outlook.com으로 전자 메일을 보내거나 github에 메시지를 남겨주십시오. ), 역할은 아래에 소개됩니다.

인증 로그인 프로세스는 다음 4 단계로 구성됩니다.

1. 제 3 자 APP는 vaswallet을 활성화합니다.

2. 권한 부여를 승인하기 위해 사용자가 권한 부여 페이지에 들어가도록 안내하십시오 AID 사용자는을 클릭하여 권한 부여를 확인하고 openCode를 얻습니다.

   3, 지갑 APP는 액세스 당사자 APP 또는 H5 배경에 openCode를 반환합니다

4. 액세스 당사자는 openCode 및 accessToken과 appId (타사 APP 응용 프로그램 이후에 얻은 UUID)를 사용하여 월렛에 사용자 보조 및 주소를 백그라운드에서 얻도록 요청합니다.

## 인증 된 로그인

### 앱 인증 로그인

#### 	1、APP는 권한 부여 로그인을 신청하기 위해 지갑을 호출합니다.

타사 APP는 SchemeURL을 사용하여 월렛 앱을 선택하고 URL 인코딩 된 JSON을 사용하여 다음 매개 변수를 전달합니다.

| KEY   | 설명                        |
| ----- | ----------------------------- |
| type  | param:  login  필수 |
| uuid | 액세스 서비스가 열릴 때 얻은 앱의 UUID가 필요합니다. |
| callbackUrl | schemeurl 형식의 콜백 주소는 schemeurl 다음에 비워 둘 수 없습니다. 필수입니다. 예를 들면 다음과 같습니다. appsmeme : // login |
| callbackType | 0이면 openCode 로의 리턴은 "?"또는 "&"로 연결됩니다. openCode는 callbackUrl의 끝에 1 회 직접 접속되며 기본값 0은 필요하지 않습니다. |
| openCallBackType | 플랫폼이 지정되지 않았거나 플랫폼이 모바일 인 경우에 효과적입니다.<br />openCallBackType이 0이거나 비어 있으면 월렛에서 callbackurl 애플리케이션을 엽니 다.<br />openCallBackType이 1 인 경우 월렛은 콜백 URL에 대한 http 요청을 시작하고 월렛을 최소화합니다.<br />불필요 |
| platform | 지갑의 단말기를 조정하십시오. 전화 내에서 전화를 받으면 모바일을 작성하십시오. <br />웹 페이지를 작성하는 웹 페이지 QR 코드 인 경우 |

> IOS 시스템은 스킴에서 특수 기호를 사용할 수 없으므로 JSON 데이터를 URL로 묶어야합니다.
>
> callbackurl 자체의 매개 변수에 특수 문자가 표시되면 내부 매개 변수를 미리 URL 인코딩해야합니다.

전체 URL 인코딩 전

```
vaswallet://{"type":"login","uuid":"121212-121212-121212","callbackUrl":"appscheme://login/a/b?d=1&redirect=http%3A%2F%2Fwww.google.com%2Fs%3Fwd%3D12345"}
```

전체 URL이 인코딩 된 후

```bash
vaswallet://%7B%22type%22:%22login%22,%22uuid%22:%22121212-121212-121212%22,%22callbackUrl%22:%22appscheme://login/a/b?d=1&redirect=http%253A%252F%252Fwww.google.com%252Fs%253Fwd%253D12345%22%7D
```

#### 2、입수AID와Address

​		사용자가 VAS 월렛에서 권한 부여를 허용 한 후 월렛은 권한있는 openCode를 생성하고 openCode 매개 변수를 전달하여 callbackUrl의 주소로 이동합니다.

> openCode는 1 분 동안 유효하며 한 번 사용한 후 만료됩니다

openCode를 얻은 후 다음 링크를 요청하여 도움과 주소를 얻으십시오.

https://mobile.vasblock.com/vas/server/oauth/login/aidInfo?openCode={openCode}&accessToken={accessToken}&appId={appId}

| 매개 변수 이름 | 설명                                             |
| -------------- | ------------------------------------------------ |
| openCode       | openCode를 얻으려면 첫 번째 단계를 작성하십시오. |
| accessToken    | 타사 액세스가 활성화되면 획득 한 AccessToken     |
| appId          | 타사 액세스가 활성화 될 때 얻은 Uuid             |

반품 지침

올바른 경우 반환되는 JSON 패킷은 다음과 같습니다.

```json
{
    "result":{
        "address":"*******",
        "aid":"*********"
    },
    "success":true,
    "errorCode":""
}
```

오류 리턴 코드 errorCode는 다음과 같이 설명됩니다.

| 오류 코드 | 설명                                          |
| --------- | --------------------------------------------- |
| 900001    | accessToken 오류                              |
| 010001    | 알 수없는 오류                                |
| 900002    | 쿼리 시간이 초과되었거나 AID 정보가 없습니다. |

### WEB 스캔 코드 인증 로그인

#### 1、QR 코드 생성

액세스 측은 VAS 월렛 스캔 코드 로그인을 제공하기 위해 URL QR 코드를 제공해야합니다. URL 매개 변수는 다음과 같습니다

| 매개 변수 이름 | 설명    |필수 여부|
| -------- | --------- |---------|
| type     | 기입 login |y|
| platform | 기입 web  |y|
| callbackUrl | 인증 후 콜백 주소가 openCode로 가져옴 |y|
| uuid | 액세스 애플리케이션이 승인 된 후 Uuid |y|
| callbackType | 0이면 "?"또는 "&"로 openCode 및 webToken이 반환됩니다. callCodeUrl의 끝에 openCode와 webToken이 1 회 직접 연결됩니다. |n|
| webToken | 액세스 측은 인증 된 로그인 사용자 주소를 확인합니다. |n|

예를 들어:

```shell
vaslogin://{"type": "login","platform": "web","callbackUrl": "https://www.google.com/login","uuid": "123-123-123","webToken": "SSFJIEKALSM"}
```

사용자가 권한을 확인한 후 월렛 APP는 QR 코드의 openopenCode 및 webToken을 callbackUrl 매개 변수 주소로 다시 가져옵니다.

> openCode는 1 분 동안 유효하며 한 번 사용한 후 만료됩니다

#### 2、입수AID와Address

openCode를 얻은 후 다음 링크를 요청하여 도움과 주소를 얻으십시오.

https://mobile.vasblock.com/vas/server/oauth/login/aidInfo?openCode={openCode}&accessToken={accessToken}&appId={appId}

| 매개 변수 이름 | 설명                                             |
| -------------- | ------------------------------------------------ |
| openCode       | openCode를 얻으려면 첫 번째 단계를 작성하십시오. |
| accessToken    | 타사 액세스가 활성화되면 획득 한 AccessToken     |
| appId          | 타사 액세스가 활성화 될 때 얻은 Uuid             |

반품 지침

올바른 경우 반환되는 JSON 패킷은 다음과 같습니다.

```json
{
    "result":{
    	"aid":"aid",
    	"address":"address"
    },
    "success":true,
    "errorCode":"",
    "errorMsg":""
 }
```

오류 리턴 코드 errorCode는 다음과 같이 설명됩니다.

| 오류 코드 | 설명                                          |
| --------- | --------------------------------------------- |
| 900001    | accessToken 오류                              |
| 010001    | 알 수없는 오류                                |
| 900002    | 쿼리 시간이 초과되었거나 AID 정보가 없습니다. |

## 월렛 결제

써드 파티 APP은 VAS 지불을 호출하여 주문 프로세스를 완료 할 수 있습니다.

### 1、사용자 결제 신청

#### 1.1APP 전화

APP 월렛은 schemeurl을 사용하여 VAS 월렛을 올리고 URL 인코딩 된 JSON 오브젝트를 통해 다음 매개 변수를 전달합니다.

| 매개 변수 이름 | 설명                                                       |
| ---------------- | ------------------------------------------------------------ |
| type             | 타입. 지불을 작성                                      |
| appUuid             | 앱이 적용될 때 Uuid 여야합니다.               |
| orderId          | 주문 ID는 필수입니다.                    |
| toAddr           | 수신자 주소                                       |
| assetType        | 지불 자산의 유형이어야합니다.                         |
| amount           | 지불 금액은                                       |
| callbackUrl      | 결제 성공 VAS 서비스는 결제 상태에 대한 백그라운드 콜백을 통지합니다 호스트는 보안 도메인 이름 내에 있어야하며 반드시 있어야합니다. |
| callbackScheme   | 결제 완료 후 반환되는 appchemeurl은 반드시 있어야합니다. |
| description             | 설명이 사용자에게 제공됩니다.                          |
| signedContent    | 서명 데이터. 액세스하는 동안 얻은 rsa 개인 키를 사용하여 매개 변수에 서명해야합니다. |
| openCallBackType | 결제 완료 후 APP 콜백 방법. 0이면 전화기가 callbackScheme을 엽니 다. 다른 APP가 한 번에 열리지 않으면 월렛 APP 만 최소화되며이 조작은 지불 상태 콜백에 영향을 미치지 않습니다. |

예 :

URL 인코딩 전

```
vaswallet://{"type": "pay","appUuid": "8888-9999-1111-6666","orderId": "201911087855441","toAddr": "*********************","assetType": "VAS","amount": "0.001","callbackUrl": "https://localhost:8080/pay?orderId=201911087855441","callbackScheme": "payApp",
	"description": "*****","signedContent": "*******","openCallBackType": "1"}
```

> 참고 : amount는 문자열 유형입니다.

URL 인코딩 후

```
vaswallet://%7B%22type%22:%20%22pay%22,%22appUuid%22:%20%228888-9999-1111-6666%22,%22orderId%22:%20%22201911087855441%22,%22toAddr%22:%20%22*********************%22,%22assetType%22:%20%22VAS%22,%22amount%22:%20%220.001%22,%22callbackUrl%22:%20%22https://localhost:8080/pay?orderId=201911087855441%22,%22callbackScheme%22:%20%22payApp%22,%0A%09%22description%22:%20%22*****%22,%22signedContent%22:%20%22*******%22,%22openCallBackType%22:%20%221%22%7D
```

#### 1.2QR 코드 호출

액세스 당사자는 월렛 APP에서 코드를 스캔하기 위해 URL QR 코드를 구성해야합니다. 매개 변수는 json 방법을 사용하며 내용은 다음과 같습니다.

| 매개 변수 이름 | 설명                                                         |
| -------------- | ------------------------------------------------------------ |
| type           | 지불을 작성                                                  |
| signedContent  | 서명 데이터. 액세스하는 동안 얻은 rsa 개인 키를 사용하여 매개 변수에 서명해야합니다. |
| appUuid        | 개봉 당시 얻은 appuuid는 있어야합니다.                       |
| orderId        | 타사 비즈니스 시스템 주문 번호 여야합니다.                   |
| toAddr         | 소장 주소                                                    |
| assetType      | 지불 자산의 유형이어야합니다.                                |
| amount         | 지불 금액이되어야합니다.                                     |
| callbackUrl    | 결제 성공 VAS 서비스는 결제 상태에 대한 백그라운드 콜백을 통지합니다 호스트는 보안 도메인 이름 내에 있어야하며 반드시 있어야합니다. |
| description    | 설명이 사용자에게 제공됩니다.                                |

예를 들어

```
vaspay://{"type": "pay","appUuid": "8888-9999-1111-6666","orderId": "201911087855441","toAddr": "*********************","assetType": "VAS","amount": "0.001","callbackUrl": "https://localhost:8080/pay?orderId=201911087855441","description": "*****","signedContent": "*******",}
```



#### 서명 알고리즘

애플리케이션시 rsaPrivate를 사용하여 SHA1WithRSA 알고리즘을 사용하여 마운트 양, assetType, callbackUrl, orderId, toAddr, uuid에 서명하십시오.

서명 될 매개 변수가 다음과 같다고 가정하십시오.

```json
{"amount":"amountVal","appUuid":"uuidVal","assetType":"assetTypeVal","callbackUrl":"callbackUrlVal","orderId":"orderIdVal","toAddr":"toAddrVal"}
```

##### 1.amount,appUuid,assetType,callbackUrl,orderId,toAddr 순서로 매개 변수를 json 구조로 배열하십시오.

##### 2.이 매개 변수는 SHA1withRSA로 서명되고 서명 된 내용은 rsaContent로 BASE64로 인코딩됩니다.

### 2、결제가 완료되면 원래 APP로 돌아가 백그라운드에서 결제 상태를 알립니다.

결제가 성공하고 블록이 확인되면 GET 양식 요청이 callbackUrl로 전송됩니다.

CallbackUrl에 포함 된 매개 변수는 다음과 같습니다.

| 매개 변수 이름 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| uuid           | 결제 서비스가 열릴 때 적용되는 AppUuid                       |
| orderId        | 타사 비즈니스 시스템 주문 번호                               |
| fromAddr       | 지불 주소                                                    |
| toAddr         | 수집 주소                                                    |
| assetType      | 결제 자산 유형                                               |
| amount         | 지불 금액, 문자열 유형                                       |
| txid           | 결제 성공 후 체인 거래 번호                                  |
| callbackUrl    | 결제 성공 VAS 서비스는 결제 상태의 백그라운드 콜백 경로를 알려줍니다. |
| rsaContent     | 서명 데이터, 액세스 중에 얻은 rsa 개인 키를 사용하여 매개 변수에 서명 |


### 서명 알고리즘

APP 애플리케이션을 사용할 때 rsaPrivate는 SHA1WithRSA 알고리즘을 사용하여 매개 변수에 서명합니다.

##### 1.매개 변수는 수량, assetType, callbackUrl, fromAddr, orderId, toAddr, txid, uuid 순서로 json 구조로 배열됩니다.

> 금액은 JSON으로 직접 문자열 유형이며 부동 소수점 문자열 (1.00000)로 설정하지 않습니다

##### 2.이 매개 변수는 SHA1withRSA로 서명되고 서명 된 내용은 rsaContent로 BASE64로 인코딩됩니다. 타사 dapp은 공개 키를 사용하여 매개 변수 및 서명 된 정보를 확인할 수 있습니다.

타사 Dapp이 반환해야하는 정보는 다음과 같습니다.

| 반환 필드 | 설명                                               |
| --------- | -------------------------------------------------- |
| success   | 참 또는 거짓, 참이면 콜백 성공, 거짓이면 콜백 실패 |
| errorCode | 오류 코드, 성공하면 반환 할 수 없습니다.           |
| errorMsg  | 오류 메시지, 성공하면 반환 할 수 없습니다.         |



## 현재 가격 문의

인터페이스 주소

```
[POST] https://mobile.vasblock.com/vas/server/oauth/pay/price?currency=USD&accessToken=000000
```

여기서 가격은 예치금 풀 가격의 비 이차 시장 가격입니다.

주파수 제한 : 분당 10 회

인터페이스 파라미터

| 매개 변수 이름 | 파라미터 설명                                        | 필수 여부 |
| -------------- | ---------------------------------------------------- | --------- |
| currency       | 통화 유형. 현재 CNY, USD, EUR, GBP, JPY, KRW 만 지원 | y         |
| accessToken    | 접근 자 accessToken                                  | y         |

반환 결과

| 이름      | 설명                                                         |
| --------- | ------------------------------------------------------------ |
| success   | 참 또는 거짓, 참이면 호출 성공, 거짓이면 호출이 올바르지 않음 |
| errorCode | 오류 코드 성공이 거짓 일 때 유효                             |
| errorMsg  | 오류 메시지 성공이 거짓 일 때 유효                           |
| result    | 성공한 경우 지정된 통화의 현재 가격을 가져 오십시오. 그렇지 않으면 비어 있습니다. |

오류 코드

| 오류 코드 | 설명                   |
| --------- | ---------------------- |
| 900013    | accessToken 오류       |
| 900014    | 가격을 얻지 못했습니다 |
| 900015    | 잘못된 통화 유형       |
