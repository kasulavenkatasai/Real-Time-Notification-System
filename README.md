# Real-Time-Notification-System
A serverless real-time notification system built using AWS Lambda, Amazon SNS, and Amazon DynamoDB.   It automatically stores event data in DynamoDB and triggers instant notifications to subscribers.
## 🚀 Features
- **Serverless architecture** – no servers to manage.
- **Real-time alerts** via Amazon SNS.
- **Scalable** – can handle thousands of events without infrastructure changes.
- **Persistent storage** in DynamoDB for historical records.

---

## 🛠 Architecture
1. **Event Trigger**: A simulated event (or API request) triggers the Lambda function.
2. **Lambda Function**:
   - Writes the event data to **DynamoDB**.
   - Publishes a notification message to an **SNS Topic**.
3. **SNS Topic**:
   - Sends notifications to subscribed endpoints (Email, SMS, or HTTP).
4. **DynamoDB**:
   - Stores event details for later retrieval.

---

## 📌 AWS Services Used
- **AWS Lambda** – Serverless compute for processing events.
- **Amazon SNS** – Instant notifications delivery.
- **Amazon DynamoDB** – NoSQL database for storing event logs.
- **IAM** – Permissions & security.

- 
---

## ⚙️ Setup Instructions

### 1️⃣ Create DynamoDB Table
- Table name: `Notifications`
- Primary key: `NotificationId` (String)

### 2️⃣ Create an SNS Topic
- Name: `RealTimeAlerts`
- Add an Email/SMS subscription.

### 3️⃣ Create a Lambda Function
- Runtime: **Node.js 18.x** (or Python if preferred)
- Permissions:
  - `dynamodb:PutItem`
  - `sns:Publish`

### 4️⃣ Deploy Lambda Code
Example **Node.js Lambda**:
```javascript
const AWS = require("aws-sdk");
const dynamoDB = new AWS.DynamoDB.DocumentClient();
const sns = new AWS.SNS();

exports.handler = async (event) => {
    const notificationId = Date.now().toString();
    const message = event.message || "New Alert!";
    
    // Save to DynamoDB
    await dynamoDB.put({
        TableName: "Notifications",
        Item: {
            NotificationId: notificationId,
            Message: message,
            Timestamp: new Date().toISOString()
        }
    }).promise();
    
    // Publish to SNS
    await sns.publish({
        TopicArn: process.env.SNS_TOPIC_ARN,
        Message: message
    }).promise();

    return { statusCode: 200, body: "Notification sent!" };
};

Example Output
DynamoDB Item:
{
  "NotificationId": "1723528174000",
  "Message": "Server CPU usage exceeded 80%",
  "Timestamp": "2025-08-13T09:22:54.000Z"
}

Email Notification:
Subject: New Alert
Message: Server CPU usage exceeded 80%
 
📌 Future Improvements
Integrate with Amazon EventBridge for scheduling.

Add API Gateway for external triggers.

Support multiple notification channels.



