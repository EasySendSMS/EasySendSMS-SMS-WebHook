
# WebHook (HTTP Post Back)

Upon delivery confirmation, [EasySendSMS](https://easysendsms.com) will Post Back to your own server's Webhook URL as Raw Content, allowing you to monitor the status of your sent messages.

## Overview

In order for our system to validate your URL, your Webhook URL's HTTP Status must be `200` with no errors.

You can add your Webhook URL in your account settings under the Webhook Section. Your Webhook URL must accept GET, POST, and HEAD methods, and must have an HTTP status `200` with no error in your page code. Our system will check the URL before it can be added to your account.

In your Webhook URL code, you can ignore the GET & HEAD requests that come from our system (used only for our system to validate your URL), and just listen to the POST Raw Content.

**Example:**

```
http://www.yourdomain.com/yourwebhhok_url.php
```

Once your Webhook URL is successfully added to our system, you will receive real-time DLR (Delivery Receipt) to your end.

When specific events occur, our SMS system will post back the data to your Webhook URL. These requests are called Webhooks or status callbacks. You can then save the post back body content to your database or file for further processing.

## Post Back (Raw Content) Format

| Field       | Description                                                               |
|-------------|---------------------------------------------------------------------------|
| source      | The message sender name                                                   |
| msisdn      | Destination number of the sent message                                    |
| sent_date   | Date and time when the delivery report is received. Date format: `YYYY-MM-DD`, Time format: `hh:mm:ss` |
| sms_id      | Unique identification for a message given at the time of submission       |
| response    | Status of the SMS message (As per the table below)                        |

## SMS Status

The following are the possible statuses of your SMS messages, providing insight into the delivery process:

| Status   | Description                                                               |
|----------|---------------------------------------------------------------------------|
| DELIVRD  | The message has been received by the handset.                             |
| EXPIRED  | The carrier has timed out.                                                |
| UNDELIV  | The message failed to reach the handset.                                  |

## Response Raw Content Example

```plaintext
source=test&msisdn=12345678900&response=DELIVRD&sent_date=5%2f24%2f2024+11%3a23%3a34+PM&sms_id=fd940f38-151d-47f1-81b8-22d1220f7a55
```
