## Introduction
The NETOPIA Payment PHP library provides easy access to the NETOPIA Payments API from
applications written in the PHP language.

## Compatible
PHP 5.7.x - 8.0.x

### API URL
* #### Sandbox:
       https://secure.sandbox.netopia-payments.com/                     // ????
       https://secure.sandbox.netopia-payments.com/payment/card/start   // ????
* #### Live:
       https://secure.netopia-payments.com/                             // ????
       https://secure.mobilpay.ro/pay/payment/card/start                // ????


### API Specification
* https://secure.netopia-payments.com/spec


### API Actions
* #### **1) Start the transaction**
        
    To start the transaction you will need to send the request to **START** end point,

    * **Action URL:** /payment/card/start
    * **Method:** `POST`    
    * **Param type:** JSON
        * **Param structure:** The JSON as parameter has tree main part        
            * **Config:**  to set configiguraton section of a transaction
            * **Payment:** to set payment method and card informations of a transaction
            * **Order:** to set order details for a transction
        * **Sample JSON:**

        ```

        {
            "config": {
                "emailTemplate": "",
                "notifyUrl": "http://yourdomain.com/ipn.php",
                "redirectUrl": "",
                "language": "RO"
            },
            "payment": {
                "options": {
                "installments": 1,
                "bonus": 0
                },
                "instrument": {
                "type": "card",
                "account": "9988997799669955",
                "expMonth": 1,
                "expYear": 2024,
                "secretCode": "111",
                "token": null
                },
                "data": {
                    "BROWSER_USER_AGENT": "string",
                    "OS": "string",
                    "OS_VERSION": "string",
                    "MOBILE": "string",
                    "SCREEN_POINT": "string",
                    "SCREEN_PRINT": "string",
                    "BROWSER_COLOR_DEPTH": "string",
                    "BROWSER_SCREEN_HEIGHT": "string",
                    "BROWSER_SCREEN_WIDTH": "string",
                    "BROWSER_PLUGINS": "string",
                    "BROWSER_JAVA_ENABLED": "string",
                    "BROWSER_LANGUAGE": "string",
                    "BROWSER_TZ": "string",
                    "BROWSER_TZ_OFFSET": "string",
                    "IP_ADDRESS": "string"
                }
            },
            "order": {
                "ntpID": null,
                "posSignature": "XXXX-XXXX-XXXX-XXXX-XXXX",
                "dateTime": "YYYY-MM-DDT00:00:00Z",
                "description": "Your payment description",
                "orderID": "YOUR-UNIQUE-ORDER_ID",
                "amount": 20,
                "currency": "RON",
                "billing": {
                    "email": "user@example.com",
                    "phone": "string",
                    "firstName": "string",
                    "lastName": "string",
                    "city": "string",
                    "country": 642,
                    "state": "string",
                    "postalCode": "string",
                    "details": "string"
                },
                "shipping": {
                    "email": "user@example.com",
                    "phone": "string",
                    "firstName": "string",
                    "lastName": "string",
                    "city": "string",
                    "country": 642,
                    "state": "string",
                    "postalCode": "string",
                    "details": "string"
                },
                "products": [
                {
                    "name": "Tshirt01",
                    "code": "string123",
                    "category": "fashion",
                    "price": 10,
                    "vat": 0
                },
                {
                    "name": "Tshirt02",
                    "code": "string123",
                    "category": "fashion",
                    "price": 10,
                    "vat": 0
                }
                ],
                "installments": {
                "selected": 0,
                "available": [
                    0
                ]
                },
            "data": {
                "property1": "string",
                "property2": "string"
                }
            }
        }

        ```

        
    #### Getting start

    ```php
        $request = new Request();
        $request->posSignature  = 'XXXX-XXXX-XXXX-XXXX-XXXX';                                 // Your signiture ID hear
        $request->apiKey        = 'ApiKey_GENERATE-YOUR-KEY-FROM-MobilPay-AND-USE-IT-HEAR';   // Your API key hear
        $request->isLive        = false;                                                      // false for SANDBOX & true for LIVE
        $request->notifyUrl     = 'http://your-domain.com/ipn.php';                           // Path of your IPN
        $request->redirectUrl   = null;
    ```



    #### Start action response
    The response of **START** endpoint, will be a Json with following structure

    ```
        {
            "customerAction": {
                "authenticationToken": "YOUR-UNIQUE-AUTHENTICATION-TOKEN-PER-REQUEST",
                "formData": {
                "backUrl": "",
                "paReq": "UNIQUE-paReq-ID"                                                      // Unique "paReq" ID
                },
                "type": "Authentication3D",
                "url": "https://BankAuthentication3D:0000/ABC/DEF"                              // the bank 3D authentication URL
            },
            "error": {
                "code": "100",
                "message": "Approved"
            },
            "payment": {
                "amount": 20,
                "currency": "RON",
                "ntpID": "1234567",                                                             // Unique transaction ID
                "status": 15
            }
        }

    ```

    the following items, you will need for next steps

    * **authenticationToken** 

    * **paReq** 

    * **ntpID**

            

    Regarding the Error code & Status code continuing the process 
        
    #### **Error** codes

    * **100** : Set authenticationToken & ntpID to session

    * **56**  : duplicated Order ID

    * **99**  : There is another order with a different price

    * **19**  : Expire Card Error

    * **20**  : Founduri Error

    * **21**  : CVV Error

    * **22**  : CVV Error

    * **34**  : Card Tranzactie nepermisa Error

    * **0**   : Card has no 3DS
        
    #### **Status** codes

    * **15** : need authorize
    * **3** : is paid
    * **5** : is confirmed


<hr>


* #### **2) Authorize the 3D card**

    For **authorize** of 3D card, you will need to send a HTTP request via **Form** by **POST** method to the Bank authentication URL.

    you have the Bank authentication **URL** from response previous action on **customerAction -> url**

    * **Params:**
        * paReq 
        * backUrl
        * apiKey

    #### Authorize 3D card

    ```php
        $authorize = new Authorize();
        $authorize->apiKey  = 'ApiKey_GENERATE-YOUR-KEY-FROM-MobilPay-AND-USE-IT-HEAR';   // Put Your key here
        $authorize->backUrl = 'THE-BACK-URL';
        $authorize->paReq   = "THE-UNIQUE-paReq-ID";
        $authorize->bankUrl = "THE-BANK-URL";

        $authorize->validateParam();
    ```
The **backUrl** used by client's bank to return data to your site/app,
The bank will returen a token called **paRes**  


<hr>


* #### **3) Verify authentication**
    To verify authentication you will need to send the request to **verify-auth** end point,

    * **Action URL:** /payment/card/verify-auth
    * **Method:** `POST`    
    * **Param type:** JSON
        * **Params:**         
            * **authenticationToken:**  The unique authentication token, from **start** action
            * **ntpID:** The transaction id from **start** action
            * **paRes:** The **paRes** from client's bank
        * **Sample JSON:**

        ```
        {
        "authenticationToken": "YOUR-UNIQUE-AUTHENTICATION-TOKEN-PER-REQUEST",
        "ntpID": "1234567",
        "formData": 
            {
                "paRes": "THE-paRes-ID-WHAT-YOU-RECIVE-IT-FROM-THE-BANK"
            }
        }
        ```

    #### Example of verify-auth

    ```php
        $verifyAuth = new VerifyAuth();
        $verifyAuth->apiKey              = 'ApiKey_GENERATE-YOUR-KEY-FROM-MobilPay-AND-USE-IT-HEAR';
        $verifyAuth->authenticationToken = 'YOUR-UNIQUE-AUTHENTICATION-TOKEN-PER-REQUEST';
        $verifyAuth->ntpID               = 'THE-UNIQUE-TRANSACTION-ID';
        $verifyAuth->paRes               = 'THE-paRes-ID-WHAT-YOU-RECIVE-IT-FROM-THE-BANK';
        $verifyAuth->isLive              = false;       // FALSE for SANDBOX & TRUE for LIVE mode

        $jsonAuthParam = $verifyAuth->setVerifyAuth();  // To set parameters for /payment/card/verify-auth

        $paymentResult = $verifyAuth->sendRequestVerifyAuth($jsonAuthParam);  // To send request to /payment/card/verify-auth

    ```

    #### verify-auth action response
    The response of **verify-auth** endpoint, will be a Json with following structure

    ```
        {
        "error": {
            "code": "00",
            "message": "Approved"
        },
        "payment": 
            {
                "amount": 20,
                "currency": "RON",
                "data": {
                "AuthCode": "A-UNIQUE-CODE",
                "BIN": "000000",
                "ISSUER": "Netopia GB",
                "ISSUER_COUNTRY": "COUNTRY-ID",
                "RRN": "A-UNIQUE-CODE"
                },
                "ntpID": "1234567",
                "status": 3,
                "token": "A-UNIQUE-TOKEN"
            }
        }

    ```

    Regarding the **error code** & the **status** you will be able to manage the messages & the actions on your Site / App after the success or failed payments in 3DS

#### What is 3DS
3DS is a security protocol used to authenticate users / card holders.
*   #### What kind of data need to be collected for 3DS 
    To have benefit of 3DS need to be collected some simple data of the User's device, what they used it to make the payments 

    Like : 
        OS name, OS version, IP, ...
        
    In above **simple Json**, you have a list of them (payment -> data -> 3DS) 
    Feel free to use <a href="#">**/example/theme/js/3DS.js**</a> if you need it.
##### Resources
###### ( <a href="https://github.com/mobilpay" target="_blank">GitHub repository</a> )
###### ( <a href="https://documenter.postman.com/preview/4914690-b18061f3-e352-4d30-a4f5-1195dcfc40e7?environment=&versionTag=latest&apiName=CURRENT&version=latest&documentationLayout=classic-double-column&right-sidebar=303030&top-bar=FFFFFF&highlight=EF5B25" target="_blank">see API examples in different programming languages</a> )