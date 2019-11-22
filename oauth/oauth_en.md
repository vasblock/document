# VAS authorized access to developer documentation

Pay tribute to all developers here, we are changing the world!

Users can obtain basic user information and receive user payment through the VAS authorization mechanism in a third-party webpage or APP, thereby implementing business logic and creating a VAS ecosystem.

The skirt of VAS wallet is vaswallet.

## Apply for authorized access

The access party needs to fill in the relevant third-party application information in the vas wallet authorization application, and the community will feedback the access situation in the APP after passing the review.

> The authorized callback domain name configuration specification is a full domain name. For example, the domain name required for webpage authorization is: www.vasblock.com, and the following page of the domain name is configured http://www.vasblock.com/music.html, http://www Authorization can be done at .vasblock.com/login.html. However, http://pay.vasblock.com, http://music.vasblock.com, and http://vasblock.com cannot perform authorization operations.
>
> If it is the authorization operation initiated by the mobile phone H5, please fill in the http or https at the scheme input and fill in the security callback domain name.

After the access application is approved, uuid, accessToken, rsaPrivate, rsaPublic will be obtained. (Click "View Details" in the DAPP access application record to view. If you have any questions, please send an email to vas.github@outlook.com or leave a message on github. ), the role will be introduced below.

The authorization login process is divided into four steps:

1. The third party APP activates vaswallet.

2. Guide the user to enter the authorization page to approve the authorization. The AID user clicks to confirm the authorization and obtains the openCode.

3. The wallet APP returns openCode to the access party APP or H5 background.

4. The access party uses the openCode and accessToken and the appId (uuid obtained after the third-party APP application) to request the wallet to obtain the user aid and address in the background.

## Authorized login

### App authorized login

#### 	1, APP calls the wallet to apply for authorization to log in

The third-party APP uses the SchemeURL to pick up the wallet app and uses the URL-encoded JSON to pass the following parameters.

| KEY   | Description               |
| ----- | ----------------------------- |
| type  | Login, "login" required |
| uuid | Uuid of the app obtained when the access service is activated, required |
| callbackUrl | The callback address in the schemeurl format cannot be empty after the schemeurl. It is required. For example: appsmeme://login |
| callbackType | When 0, the return to openCode will be spliced by "?" or "&". For 1 time, openCode is directly spliced at the end of callbackUrl. Default 0, not required. |
| openCallBackType | Effective when platform is not specified or when platform is mobile. <br /> When openCallBackType is 0 or empty, the wallet opens the callbackurl application. <br /> When openCallBackType is 1, the wallet initiates an http request to the callbackurl and minimizes the wallet. <br /> not required |
| platform | Tune up the wallet's terminal. If it is called within the phone to fill in mobile <br /> If it is to scan the web QR code to fill in the web |

> Since the IOS system does not allow the use of special symbols in the scheme, it is necessary to url the JSON data.
>
> It should be noted that if special characters appear in the parameters of the callbackurl itself, the internal parameters must be url encoded in advance.

Before the overall URL encoding

```
vaswallet://{"type":"login","uuid":"121212-121212-121212","callbackUrl":"appscheme://login/a/b?d=1&redirect=http%3A%2F%2Fwww.google.com%2Fs%3Fwd%3D12345"}
```

After the overall URL is encoded

```bash
vaswallet://%7B%22type%22:%22login%22,%22uuid%22:%22121212-121212-121212%22,%22callbackUrl%22:%22appscheme://login/a/b?d=1&redirect=http%253A%252F%252Fwww.google.com%252Fs%253Fwd%253D12345%22%7D
```

#### 2, get AID and Address

After the user allows authorization in the VAS wallet, the wallet generates an authorized openCode and carries the openCode parameter to jump to the address of the callbackUrl.

> openCode is valid for 1 minute and expires after one use

After obtaining openCode, request the following link to get the aid and address.

https://mobile.vasblock.com/vas/server/oauth/login/aidInfo?openCode={openCode}&accessToken={accessToken}&appId={appId}

| Parameter   | Description                                               |
| ----------- | --------------------------------------------------------- |
| openCode    | Fill in the first step to get the openCode                |
| accessToken | AccessToken obtained when third-party access is activated |
| appId       | Uuid obtained when third-party access is activated        |

The JSON packet returned when correct is as follows

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

**Error return code errorCode is as follows: **

| ErrorCode | Description                                                  |
| --------- | ------------------------------------------------------------ |
| 900001    | accessToken error                                            |
| 010001    | unknown mistake                                              |
| 900002    | The query has timed out or the AID information does not exist. |

### WEB scan code authorized login

#### 1. create a QR code

The access side needs to provide the URL QR code to supply the VAS wallet scan code login. URL parameters are as follows.

| Parameter | Description |Required|
| -------- | --------- |---------|
| type     | "login" |是|
| platform | "web" |是|
| callbackUrl | Callback address brought to openCode after authorization |是|
| uuid | Uuid after the access application is approved |是|
| callbackType | When 0, the return of openCode and webToken will be spliced by "?" or "&". For 1 time, openCode and webToken are directly spliced at the end of callbackUrl. Default 0 |否|
| webToken | The access side verifies the authorized login user address. |否|

E.g:

```shell
vaslogin://{"type": "login","platform": "web","callbackUrl": "https://www.google.com/login","uuid": "123-123-123","webToken": "SSFJIEKALSM"}
```

After the user confirms the authorization, the wallet APP will bring the openopenCode and the webToken in the QR code back to the callbackUrl parameter address.

> openCode is valid for 1 minute and expires after one use

#### 2. get AID and Address

After obtaining openCode, request the following link to get the aid and address

https://mobile.vasblock.com/vas/server/oauth/login/aidInfo?openCode={openCode}&accessToken={accessToken}&appId={appId}

| Parameter   | Description                                               |
| ----------- | --------------------------------------------------------- |
| openCode    | Fill in the first step to get the openCode                |
| accessToken | AccessToken obtained when third-party access is activated |
| appId       | Uuid obtained when third-party access is activated        |

The JSON packet returned when correct is as follows

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

**Error return code errorCode is as follows: **

| ErrorCode | Description                                                  |
| --------- | ------------------------------------------------------------ |
| 900001    | accessToken error                                            |
| 010001    | unknown mistake                                              |
| 900002    | The query has timed out or the AID information does not exist. |



## Wallet payment

The third-party APP can invoke the VAS payment to complete the process of placing the order.

### 1. Apply for user payment

#### 1.1APP call

The APP wallet uses the schemeurl to raise the VAS wallet and passes the following parameters via the URL-encoded JSON object.

| Parameter | Description                                              |
| ---------------- | ------------------------------------------------------------ |
| type             | "pay"                                          |
| appUuid             | Uuid when the app is applied, required. |
| orderId          | order id, required.               |
| toAddr           | Receiver address, required.                  |
| assetType        | Payment asset type,required.             |
| amount           | amount,required.                            |
| callbackUrl      | The payment is successful. The VAS service notifies the background callback of the payment status. The host needs to be in the secure domain name.required. |
| callbackScheme   | Appschemeurl that jumps back after the payment is completed, required. |
| description             | Description,Will be shown to the user.    |
| signedContent    | Signature data. Sign the parameters using the rsa private key obtained during access.required. |
| openCallBackType | APP callback method after payment is completed. When 0, the phone opens the callbackScheme. If the other APP is not opened at 1 time, only the wallet APP is minimized, and this operation does not affect the payment status callback. |

E.g.：

Before URL encoding

```
vaswallet://{"type": "pay","appUuid": "8888-9999-1111-6666","orderId": "201911087855441","toAddr": "*********************","assetType": "VAS","amount": "0.001","callbackUrl": "https://localhost:8080/pay?orderId=201911087855441","callbackScheme": "payApp",
	"description": "*****","signedContent": "*******","openCallBackType": "1"}
```

> Note: amount is a string type

After URL encoding

```
vaswallet://%7B%22type%22:%20%22pay%22,%22appUuid%22:%20%228888-9999-1111-6666%22,%22orderId%22:%20%22201911087855441%22,%22toAddr%22:%20%22*********************%22,%22assetType%22:%20%22VAS%22,%22amount%22:%20%220.001%22,%22callbackUrl%22:%20%22https://localhost:8080/pay?orderId=201911087855441%22,%22callbackScheme%22:%20%22payApp%22,%0A%09%22description%22:%20%22*****%22,%22signedContent%22:%20%22*******%22,%22openCallBackType%22:%20%221%22%7D
```

#### 1.2 two-dimensional code call

The access party needs to construct a URL QR code to scan the code by the wallet APP. The parameters use the json method, the content is as follows

| Parameter     | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| type          | "pay"                                                        |
| signedContent | Signature data. Sign the parameters using the rsa private key obtained during access, required. |
| appUuid       | Appuuid obtained at the time of opening, required.           |
| orderId       | Third-party business system order number, required.          |
| toAddr        | Receiver address, required.                                  |
| assetType     | Payment asset type,required.                                 |
| amount        | amount,required.                                             |
| callbackUrl   | The payment is successful. The VAS service notifies the background callback of the payment status. The host needs to be in the secure domain name.required. |
| description   | The description will be presented to the user.               |

e.g.：

```
vas://{"type": "pay","appUuid": "8888-9999-1111-6666","orderId": "201911087855441","toAddr": "*********************","assetType": "VAS","amount": "0.001","callbackUrl": "https://localhost:8080/pay?orderId=201911087855441","description": "*****","signedContent": "*******",}
```



#### Signature algorithm

Use the rsaPrivate at the time of application to sign the amount of mount, assetType, callbackUrl, orderId, toAddr, uuid using the SHA1WithRSA algorithm.

Suppose the parameters to be signed are as follows:

```json
{"appUuid":"uuidVal","orderId":"orderIdVal","toAddr":"toAddrVal","assetType":"assetTypeVal","amount":"amountVal","callbackUrl":"callbackUrlVal"}
```

##### 1. Arrange the parameters into a json structure in the order of amount, assetType, callbackUrl, orderId, toAddr, uuid.

##### 2. Use SHA1withRSA to sign the parameters. The signed content is BASE64 encoded as rsaContent.

### 2. After the payment is completed, jump back to the original APP and notify the payment status in the background.

If the payment is successful and the block is confirmed, a GET form request will be sent to the callbackUrl.

The parameters carried in the CallbackUrl are as follows:

| Parameter   | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| uuid        | AppUuid applied when the payment service is opened           |
| orderId     | Third-party business system order number                     |
| fromAddr    | Payment address                                              |
| toAddr      | Receiver address                                             |
| assetType   | Payment asset type                                           |
| amount      | Payment amount, string type                                  |
| txid        | The chain transaction number after successful payment        |
| callbackUrl | The payment is successful. The VAS service notifies the background callback path of the payment status. |
| rsaContent  | Signature data, sign the parameters using the rsa private key obtained during access |


### Signature algorithm

The rsaPrivate when using the APP application uses the SHA1WithRSA algorithm to sign the parameters.

##### 1. Arrange the parameters into a json structure in the order of amount, assetType, callbackUrl, fromAddr, orderId, toAddr, txid, uuid.

> Amount is a string type, directly into JSON, do not turn to floating point string (1.00000)

##### 2. The parameter is signed by SHA1withRSA, and the signed content is BASE64 encoded as rsaContent. The third-party dapp can use the public key to verify the parameters and the signed information.

The information that the third-party Dapp needs to return is as follows:

| Result    | Description                                                  |
| --------- | ------------------------------------------------------------ |
| success   | True or false, if true, the callback succeeds, if false, the callback fails |
| errorCode | Error code, if success is true, it can not be returned.      |
| errorMsg  | Error message, if success is true, it can't be returned.     |



## Current price inquiry

interface address

```
[POST] https://mobile.vasblock.com/vas/server/oauth/pay/price?currency=USD&accessToken=000000
```

Note that the price here is the non-secondary market price of the depository pool price.

Frequency limit: 10 times per minute

| Parameter   | Description                                                  | requeried |
| ----------- | ------------------------------------------------------------ | --------- |
| currency    | Currency type. Currently only supports CNY, USD, EUR, GBP, JPY, KRW | Yes       |
| accessToken | Accessor accessToken                                         | Yes       |

Result

| Result    | Description                                                  |
| --------- | ------------------------------------------------------------ |
| success   | True or false, if true, the call succeeds, if false, the call is incorrect |
| errorCode | error code. Valid when success is false                      |
| errorMsg  | Error message. Valid when success is false                   |
| result    | Get the current price of the specified currency when success is true, otherwise return empty |

Error code

| ErrorCode | Description         |
| --------- | ------------------- |
| 900013    | accessToken error   |
| 900014    | Failed to get price |
| 900015    | Wrong currency type |
