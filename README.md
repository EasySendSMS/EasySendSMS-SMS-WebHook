# WebHook (HTTP Post Back)

Upon delivery confirmation, [EasySendSMS](https://easysendsms.com) will Post Back to your own server Webhook URL a Raw Content to allow you to monitor the status of your sent messages.

In order for our system to validate your URL, your Webhook URL HTTP Status must be `200` and no any errors.

You can add your Webhook URL at your account [Settings](https://my.easysendsms.app/login) (Webhook Section).

Your Webhook URL must accept `GET`, `POST`, and `HEAD` methods and have HTTP status `200` with no error at your page code. Our system will check the URL before it can be added to your account.

In your Webhook URL code you can ignore the `GET` & `HEAD` requests which came from our system (used only for our system to validate your URL), and just listen to the `POST` Raw Content.

## What Is an SMS Webhook and Why Does It Matter?

An SMS webhook is a server-to-server HTTP callback mechanism that enables real-time communication between the SMS API gateway and your application. Instead of repeatedly polling the API to check whether a message has been delivered, a webhook pushes delivery status updates directly to your server the moment they become available. This event-driven approach significantly reduces unnecessary API calls, lowers server load, and ensures that your application always has the most up-to-date delivery information without any delay.

Webhooks are essential for businesses that rely on time-sensitive messaging workflows such as bulk SMS campaigns, OTP verification, transactional alerts, and two-factor authentication (2FA). By implementing the EasySendSMS webhook, developers can automate delivery tracking, trigger follow-up actions based on message status, and build intelligent messaging pipelines that respond dynamically to real-time events. Whether you are sending promotional messages through SMS marketing campaigns or critical system notifications, webhooks provide the reliability and speed your application demands.

## How the EasySendSMS Webhook Works

The EasySendSMS webhook system operates on a straightforward principle: when the delivery status of an SMS message changes, our platform automatically sends an HTTP POST request containing the delivery report (DLR) data to the webhook URL you have configured in your account. This process happens in real time, meaning your application receives status updates within seconds of the carrier confirming delivery, expiry, or failure of a message.

The workflow begins when you send an SMS using either the HTTP API or the REST API. Once the message is submitted to the carrier network, EasySendSMS tracks its progress. As soon as the carrier returns a delivery receipt, our system formats the data into a structured raw content payload and posts it to your webhook endpoint. Your server then parses the incoming data and can store it in a database, trigger automated workflows, update dashboards, or send follow-up messages based on the delivery outcome.

For high-throughput messaging scenarios, such as sending thousands of messages via SMPP or the bulk SMS API, webhooks ensure that every single delivery report is captured and forwarded to your system without requiring manual intervention. This makes webhooks an indispensable tool for developers building scalable, production-grade messaging applications.

## Setting Up Your Webhook URL

Configuring your webhook URL with EasySendSMS is a simple process that can be completed in just a few steps. First, ensure that your server endpoint is publicly accessible over the internet and returns an HTTP `200` status code when accessed. The endpoint must be capable of handling `GET`, `POST`, and `HEAD` request methods. Our system uses `GET` and `HEAD` requests solely for validation purposes when you first add the URL, while the actual delivery report data is sent exclusively via `POST` requests with raw content in the body.

To add your webhook URL, log in to your [EasySendSMS dashboard](https://my.easysendsms.app/login) and navigate to the Settings page, then locate the Webhook Section. Enter your full webhook URL and save the configuration. Our system will immediately validate the URL by sending a test request. If the URL returns an HTTP `200` status with no errors, it will be successfully registered and activated for receiving delivery reports.

If you encounter issues during setup, make sure your server does not block requests from external IP addresses, that there are no firewall rules preventing inbound HTTP connections, and that your endpoint does not require authentication headers for the initial validation request.

### Example Webhook URL:

```http
http://www.yourdomain.com/yourwebhook_url.php
```

Once your Webhook URL is added successfully to our system, you will receive real-time DLR to your end.

When specific events occur, our SMS system will post back the data to your own Webhook URL, these requests are called Webhooks or status callbacks. You then can save the post back body content to your DB or file, for further work.

## WebHook Response Fields

| Post Back (Raw Content) | Description |
| :--- | :--- |
| **source** | The message sender name |
| **msisdn** | Destination number of sent message |
| **sent_date** | Date and time when the delivery report is received. Date format is `YYYY-MM-DD` Time format is `hh:mm:ss` |
| **sms_id** | Unique identification for a message given at the time of submission |
| **response** | Status of the SMS message (As below table) |

### Understanding the Webhook Response Fields

Each webhook callback from EasySendSMS contains five key fields that provide complete information about the delivery status of your SMS message. The **source** field returns the sender name or sender ID that was used when the message was originally submitted. The **msisdn** field contains the recipient's phone number in international format, allowing you to match the delivery report to the correct contact in your database.

The **sent_date** field provides a precise timestamp indicating when the delivery report was generated by the carrier, formatted as `YYYY-MM-DD hh:mm:ss`. This timestamp is invaluable for analytics, audit trails, and compliance reporting. The **sms_id** is the unique message identifier that was returned when you initially submitted the SMS, serving as the primary key for correlating outbound messages with their delivery receipts. Finally, the **response** field contains the actual delivery status code, which indicates whether the message was successfully delivered, expired, or failed.

## SMS Status

The following are the possible statuses of your SMS messages, providing insight into the delivery process:

| Status | Description |
| :--- | :--- |
| **DELIVRD** | The message has been received by the handset. |
| **EXPIRED** | The carrier has timed out. |
| **UNDELIV** | The messages failed to reach the handset. |

### Interpreting SMS Delivery Statuses

Understanding each delivery status is critical for optimizing your SMS messaging strategy and maintaining high deliverability rates. A **DELIVRD** status confirms that the message has been successfully received by the recipient's mobile device. This is the ideal outcome and indicates that the carrier network, the recipient's phone, and all routing infrastructure functioned correctly. For bulk SMS campaigns, tracking the DELIVRD rate helps you measure campaign effectiveness and identify high-performing routes.

An **EXPIRED** status means that the carrier attempted to deliver the message but the recipient's device was unreachable within the validity period. This can occur when the phone is switched off, out of network coverage, or has a full message inbox. Expired messages are common in international messaging where network conditions vary across different SMS routes. Monitoring expiration rates can help you identify problematic destinations and adjust your routing strategy accordingly.

An **UNDELIV** status indicates a permanent delivery failure. This may be caused by an invalid or deactivated phone number, carrier-level content filtering, sender ID restrictions in the destination country, or network-level blocking. To reduce undelivered messages, consider validating phone numbers before sending using the HLR Lookup API. Regularly cleaning your contact lists and following SMS encoding best practices will also help improve your overall delivery rates.

## Response Raw Content

```http
source=test&msisdn=12345678900&response=DELIVRD&sent_date=5%2f24%2f2024+11%3a23%3a34+PM&sms_id=fd940f38-151d-47f1-81b8-22d1220f7a55
```

### Parsing the Raw Content in Your Application

The webhook raw content is delivered as a URL-encoded query string in the body of the HTTP POST request. To extract the individual fields, your server-side code needs to parse this query string using the standard URL decoding functions available in your programming language. For example, in PHP you can use the `$_POST` superglobal or `file_get_contents('php://input')` to read the raw body, then use `parse_str()` to convert it into an associative array. In Python, the `urllib.parse.parse_qs()` function handles the decoding, while in Node.js you can use the built-in `querystring` module or the `URLSearchParams` API.

Once parsed, you should store the delivery report data in your database for analytics, reporting, and audit purposes. A recommended approach is to create a dedicated delivery reports table indexed by **sms_id**, which allows you to efficiently join outbound message records with their corresponding delivery receipts. This data can then power real-time dashboards, automated retry logic for failed messages, and detailed campaign performance reports.

## Webhook Best Practices for Reliable SMS Delivery Tracking

To ensure your webhook integration operates reliably at scale, follow these best practices when implementing your delivery report endpoint. First, always return an HTTP `200` response immediately upon receiving the webhook payload, before performing any time-consuming processing such as database writes or external API calls. This prevents request timeouts and ensures that the EasySendSMS system registers the callback as successfully delivered. Process the data asynchronously using a message queue or background job system for optimal performance.

Second, implement idempotency in your webhook handler by using the **sms_id** field as a unique key. In rare cases, network issues may cause a webhook to be delivered more than once, and idempotent processing ensures that duplicate callbacks do not result in duplicate records or unintended side effects in your application. Additionally, implement proper error logging and monitoring for your webhook endpoint so that you can quickly identify and resolve any issues that may arise during high-volume bulk SMS sending periods.

Third, secure your webhook endpoint by validating the incoming request parameters and optionally restricting access to known IP ranges. While EasySendSMS delivers webhooks over standard HTTP, you should host your endpoint on HTTPS to encrypt data in transit and protect sensitive information such as recipient phone numbers. For enterprise-grade implementations, consider adding request signature verification or token-based authentication to your webhook handler.

## Common Webhook Use Cases

SMS webhooks unlock a wide range of automation possibilities for businesses and developers. One of the most common use cases is real-time delivery confirmation for OTP and two-factor authentication messages, where knowing that a verification code was successfully delivered is critical for user experience and security. By monitoring the webhook response, your application can automatically trigger a retry or fallback mechanism if the OTP message fails to deliver, ensuring that users are never locked out of their accounts.

Another powerful use case is campaign analytics for SMS marketing. By aggregating webhook delivery data, marketers can calculate precise delivery rates, identify underperforming destinations, and optimize their contact lists for future campaigns. Webhooks also enable automated billing and credit management systems, where delivery confirmations can trigger credit deductions and failed deliveries can initiate automatic refund processes.

For developers building SaaS platforms, CRM integrations, or e-commerce notification systems, webhooks provide the foundation for event-driven architectures that respond intelligently to message delivery outcomes. Combined with the powerful SMS API gateway features offered by EasySendSMS, including global coverage across 200+ countries and 1100+ mobile networks, webhooks enable you to build world-class messaging solutions that scale with your business.
