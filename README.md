# WebHook (HTTP Post Back)

Upon delivery confirmation, [EasySendSMS](https://www.easysendsms.com/) will Post Back to your own server Webhook URL a Raw Content to allow you to monitor the status of your sent messages.

In order for our system to validate your URL, your Webhook URL HTTP Status must be 200 and no any errors.

You can add your Webhook URL at your account [Settings](https://www.easysendsms.com/login) (Webhook Section).

Your Webhook URL must accept GET, Post, and Head methods and have HTTP status 200 with no error at your page code, our system will check the URL before it can be added to your account.

In your Webhook URL code you can ignore the GET & Head requests which came from our system (used only for our system to validate your URL), and just listen to the Post Raw Content.

### Example

```
http://www.yourdomain.com/yourwebhhok_url.php
```
once your Webhook URL is added successfully to our system, you will receive real-time DLR to your end. When specific events occur, Our SMS system will post back the data to your own Webhook URL, these requests are called Webhooks or status callbacks. You then can save the post back body content to your DB or file, for further work.



| Parameter | Description |
| --------- | ----------- |
| source | The message sender name |
| msisdn | Destination number of sent message |
| sent_date | Date and time when the delivery report is received. Date format is YYYY-MM-DD Time format is hh:mm:ss |
| sms_id | Unique identification for a message given at the time of submission |
| response | Status of the SMS message (As below table) |



### Status Codes

| Parameter | Description |
| --------- | ----------- |
| DELIVRD | The message has been received by the handset. |
| EXPIRED | The carrier has timed out. |
| UNDELIV | The messages failed to reach the handset. |
