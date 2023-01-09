# EasySendSMS Report API (DLR)

### HTTP Post Back

Upon delivery confirmation, [Easy Send SMS](https://www.easysendsms.com/) will Post Back to your own server to allow you to monitor the status of messages sent.

In order for our system to know that your URL has received the delivery notice,your page HTTP Status must be Code 200. To ensure this is the case please use the URL in your local browser before adding it in your account settings,this can be configured under Settings in your Easy Send SMS account.

### Example For Push API URL

```
http://www.yourserverdemoname.net/Demo/Demo.php 
is how a URL for each client would appear. The parameters
appended to the URL would be static. The parameters are explained below
```

| Parameter | Description |
| --------- | ----------- |
| sender | The message sender name |
| mobile | Destination number of sent message |
| date | Date and time when the delivery report is received. Date format should be YYYY-MM-DD Time format should be hh:mm:ss |
| messageid | Unique identification for a message given at the time of submission |
| status | Status of the message |

### Status Codes

| Parameter | Description |
| --------- | ----------- |
| delivrd | The message has been received by the handset. |
| expired | The carrier has timed out. |
| undeliv | The messages failed to reach the handset. |
