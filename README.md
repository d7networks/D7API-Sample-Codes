# D7 API
 Direct7 Networks is a messaging service provider helping out organizations communicate with customers in an efficient way. 
D7 Messages API allows two way messaging gateway integrations using simple HTTPs GET&POST requests.

Further, Our simplified API is compliant with both Unicode as well as normal messaging requirements, which companies can forward either as single texts or in bulk.

The following requirements must be met to enable the service
 
 - You need a D7 account - [Signup here](https://app.d7networks.com) 
 -  You must have sufficient credit on your D7 account 
 
## Method of Authentication
D7 API uses OAuth 2.0 based bearer token authentication for strong security and reliability. 
In order to keep your data secure, we highly recommend using authentication with encryption. D7 API support both TLS and SSL protocols. 

## Create app & get auth Token
The control panel includes a developer section where you can create applications. Each application has its own authentication token.
Users can create any number of applications, according to their requirements, and each application will work independently.
For creating your first application, click [here](https://app.d7networks.com/developer/applications).
Make sure you copy the authentication token generated during app creation
Also the token can be regenerated at any time by clicking [here](https://app.d7networks.com/developer/applications).

The token will look like this:
   ``` 
eyJhbGadfadfciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWsdsgQiOiJhdXRoLWJhY2adatlbmQ6YXBadsfwIiwic3ad'
 ``` 

## Revoke Token acces
For revoking access you can login to our control panel delete or recreate the app, which will invalidate all the existing tokens. 

## Features 
 * Using HTTP API you can: 
 * Send and receive SMS/MMS/WhatsApp/Viber/Telegram, 
 * Receive http callbacks for delivery notification (receipts) when SMS is received (or not) on mobile station 
 * Check status of messages and message campaigns using seperate endpoint. 
 * Send and receive text, audio, unicode/binary, multimedia  messages and receive http callbacks when a mobile station sends you a SMS-MO. 
 * Check balance 

## Channels 
 Currently we support text messages (SMS), and we are in a process of integrating MMS, Whatsapp, Viber, Telegram and other multimedia messaging platforms under same endpoint. Once its live, users can easily switch between platforms by changing single argument in messages section.  

## D7API - Check Balance

Endpoint : /messages/v1/balance

#### Request:
** Replace the "TOKEN"
   ``` 
curl --location --request GET 'http://api.beta.d7networks.com/messages/v1/balance' --header 'Accept: application/json' --header 'Authorization: Bearer TOKEN'
 ``` 
#### Response: 
##### Success
    {
    "balance": 99.9999999
    }
    
##### Unauthorized Error
```
{
    "detail": {
        "code": "ACCESS_TOKEN_SIGNATURE_VERIFICATION_FAILED",
        "message": "Access token signature verification failed"
        }
    }
```
## D7Messages - Send Message

Endpoint : /messages/v1/send 

### Request parameters 
 When calling Messges API endpoint from an application, the below parameters must be passed (at least mandatory ones) and the api will return the request id id on success. 
  
 | Parameter  | Value / Pattern  | Example(s)  | 
 | ------------ | ------------ | ------------ | 
 | *originator |  Originating address |  SMSInfo | 
 | *recipients  | Destination address'es seperated by comma in an array  |  [9715097526xx,9715097526xx] | 
 | *content  |  Message Content | Greetings from D7 API  | 
 | *channel | Messaging Channel (sms, WhatsApp, Viber, Telegram)  | sms  | 
 | report_url  | HTTP(s) URL to recieve DLR call back on message success/failure  | http://host/dlr.php  | 
 | data_coding  | Text encoding (text, unicode, auto)  |  text | 
 | msg_type  | txt, audio sms, multimedia, image   | text  | 
 
 \* Mandatory Parameters 

### Example 1 ::: same content and multiple destination

  ``` 
{
    "messages": [
        {
            "channel": "sms",
            "originator": "SMS",
            "recipients": ["+971509752655","+971509752656"],
            "content": "Greetings from D7 API ",
            "msg_type": "text"
        }
    ]
}
 ``` 
### Response 
 When the request is validated, request_id, status and created time will be returned. Users can use this request_id to query status using the Get status endpoint. 
  
##### Success 
 ``` 
 { 
 \"request_id\": \"d9835609-a4e0-10ea-a26a-eeafbe700fef\", 
   \"status\": \"accepted\", 
   \"created_at\": \"2001-08-05T12:52:38.393Z\" 
 } 
 ``` 
##### Unauthorized Error 
 ``` 
 { 
     \"detail\": { 
         \"code\": \"ACCESS_TOKEN_SIGNATURE_VERIFICATION_FAILED\", 
         \"message\": \"Access token signature verification failed\" 
     } 
 } 
 ```

##### Validation Error 
 ``` 
{
    "detail": [
        {
            "loc": [
            "string"
            ],
        "msg": "string",
        "type": "string"
        }
    ]
} 
 ```


###  Example 2 ::: Different content and multiple destinations

  ``` 
{
    "messages": [
        {
        "recipients": ["971509752666","971509752667"],
        "content": "Greetings from D7 API - First message",
        "msg_type": "text",
        "data_coding": "text"
        },
        {
        "recipients": ["971509752668","971509752669"],
        "content": "Greetings from D7 API - Second Message",
        "msg_type": "text",
        "data_coding": "text"
        }
    ],
    "message_globals": {
        "channel": "sms",
        "originator": "SMSInfo",
        "report_url": "https://the_url_to_recieve_delivery_report.com",
        "schedule_time": "2022-08-17T12:39:12",
        "schedule_utc_offset": "+04:00"
        }
}
 ``` 
 
## D7Messages - Get Message Status

Endpoint : /report/v1/message-log/{{msg_id}}

The status of the message can be requested by specifying the request ID that was returned from Send Message. 

### Sample request
** Replace the "REQUESTID" and "TOKEN"
  ``` 
curl --location --request GET 'http://api.beta.d7networks.com/report/v1/message-log/REQUESTID' --header 'Authorization: Bearer TOKEN'
 ``` 


#### Response: 
##### Success
```
{
    "request_id": "ceba9fac-838c-44c7-b67d-03a4a6352058",
    "total_recipients": 1,
    "channel": "sms",
    "messages": [
        {
            "msg_id": "8995e85e-2841-11ed-b659-0242ac160016",
            "country": "AE",
            "recipient": "+971509752655",
            "status": "processed",
            "number_of_sms": 1,
            "sms_cost": 0.0001,
            "schedule_time": null
        }
    ]
}
```    
##### Not found Error
```
{
    "detail": {
        "code": "MESSAGE_LOG_NOT_EXISTS",
        "message": "Given message request id not exists in the system. Please Try Again!"
    }
}
```
## D7 Verify - Generate OTP

Endpoint : /verify/v1/otp/send-otp

### Request parameters 
 When calling D7Verify API from an application, the below parameters must be passed (at least mandatory ones) and the api will return the request id id on success. 
  
 | Parameter  | Value / Pattern  | Example(s)  | 
 | ------------ | ------------ | ------------ | 
 | *originator |  Originating address |  SMSInfo | 
 | *recipients  | Destination address  |  +971509752555 | 
 | *content  |  OTP Message Content with {} placeholder | Greetings from D7 API, your mobile verification code is: {}  | 
 | *expiry | OTP Expiry time in seconds | 600  | 
 | *data_coding  | Text encoding (text, unicode, auto) | text | 
 
 \* Mandatory Parameters 

### Sample request body
 ``` 
{
    "originator": "SignOTP",
    "recipient": "+971509752555",
    "content": "Greetings from D7 API, your mobile verification code is: {}",
    "expiry": "600",
    "data_coding": "text"
}
 ``` 
### Response 
 When the request is validated, otp_id, status and expiry will be returned. Users can use this otp_id to regenerate otp and verify otp   
##### Success 
``` 
{
    "otp_id": "dfd31c0e-2cd2-494e-88d2-6cac05263a7f",
    "status": "OPEN",
    "expiry": 600
} 
 ``` 
##### Validation Error 
``` 
{
    "detail": [
        {
            "loc": [
                "string",
                0
            ],
            "msg": "string",
            "type": "string"
        }
    ]
}
 ```
## D7 Verify - Resend / Regenerate OTP

Endpoint : /verify/v1/otp/resend-otp

OTP can be regenerated using the otp_id which was returned from Generate OTP. 
### Sample request body
 ``` 
{
    "otp_id":"8d963dbf-d655-4fe6-9157-48885a036050"
}
 ``` 
### Response 
 When the request is validated, otp_id, status and expiry will be returned. Users can use this otp_id to regenerate otp and verify otp   
##### Success 
``` 
{
    "otp_id": "84864e9c-81f5-41af-9483-c90dffd97e4a",
    "status": "OPEN",
    "expiry": 600,
    "resend_count": 1
} 
 ``` 
##### Frequent request Error 
```
{
    "detail": "Frequent resend request, resend request need minimum 60 seconds delay"
}
```
##### Time limit expired Error 
```
{
    "detail": "Resend Failed. OTP time limit expired."
}
```

##### Validation Error 
``` 
{
    "detail": [
        {
            "loc": [
                "string",
                0
            ],
            "msg": "string",
            "type": "string"
        }
    ]
}
 ```

## D7 Verify - Verify OTP

Endpoint : /verify/v1/otp/verify-otp

Once the user recieve the OTP, it can be verified at this endpoint 
### Sample request body
 ``` 
{
    "otp_id":"8d963dbf-d655-4fe6-9157-48885a036050",
    "otp_code":435986
}
 ``` 
### Response 
 When the request is validated the status will be returned.
##### Success 
``` 
{
    "status": "APPROVED"
}
 ``` 
##### Invalid OTP Code Error 
```
{
    "detail": {
        "code": "INVALID_OTP_CODE",
        "message": "Invalid OTP code or OTP code expired"
    }
}
```
##### Validation Error 
``` 
{
    "detail": [
        {
            "loc": [
                "string",
                0
            ],
            "msg": "string",
            "type": "string"
        }
    ]
}
 ```
## D7 Verify - Get OTP Status

Endpoint : /verify/v1/report/{{otp_id}}

You can always check the OTP status at this endpoint 
### Sample request
 ** replace "OTPID" and "TOKEN"
 ``` 
curl --location --request GET 'http://api.beta.d7networks.com/verify/v1/report/OTPID' --header 'Authorization: Bearer TOKEN'
 ``` 
### Response 
 When the request is validated the status will be returned.
##### Success 
``` 
{
    "status": "OPEN"
}
 ``` 
##### Not found Error 
```
{
    "detail": {
        "code": "MESSAGE_LOG_NOT_EXISTS",
        "message": "Given message otp id not exists in the system. Please Try Again!"
    }
}
```
##### Validation Error 
``` 
{
    "detail": [
        {
            "loc": [
                "string",
                0
            ],
            "msg": "string",
            "type": "string"
        }
    ]
}
 ```
