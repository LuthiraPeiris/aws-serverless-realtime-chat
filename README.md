# ⚡ Serverless Real-Time Chat Application

A real-time chat web application built using AWS serverless services and
the WebSocket protocol.

The application allows users to establish a persistent WebSocket
connection, send messages in real time, and communicate through an
AWS-managed serverless backend.

## 🏗️ Architecture

![Image Alt](https://github.com/LuthiraPeiris/aws-serverless-realtime-chat/blob/fbf783db63da3cad84fc4f466a262c895a2ead37/architecture/diagram.png)


The application uses four core AWS services:

-   **Amazon API Gateway -- WebSocket API** --- Manages WebSocket
    connections and routes incoming messages.
-   **AWS Lambda** --- Handles WebSocket events and chat-message
    processing.
-   **Amazon DynamoDB** --- Stores chat and connection-related data.
-   **AWS IAM** --- Controls permissions between AWS services.

### Architecture Flow

``` text
┌──────────────────────┐
│     Web Browser      │
│   Chat Application    │
└──────────┬───────────┘
           │
           │ WebSocket
           ▼
┌──────────────────────────────┐
│ Amazon API Gateway           │
│        WebSocket API         │
│                              │
│ $connect                     │
│ $disconnect                  │
│ sendMessage                  │
└──────────────┬───────────────┘
               │
               │ Invoke
               ▼
┌──────────────────────────────┐
│         AWS Lambda           │
│                              │
│ • Connection handling        │
│ • Message processing         │
│ • DynamoDB interaction       │
└──────────────┬───────────────┘
               │
               │ Read / Write
               ▼
┌──────────────────────────────┐
│       Amazon DynamoDB        │
│                              │
│ Chat messages / connections  │
└──────────────────────────────┘

        AWS IAM
           │
           └── Controls permissions
               between AWS services
```

## ✨ Features

-   Real-time messaging using WebSockets
-   Serverless AWS backend
-   WebSocket connection status indicator
-   Username support
-   Message timestamps
-   Automatic reconnection when the connection is lost
-   Enter-key support for sending messages
-   Input validation and message length limits
-   Responsive chat interface
-   IAM-based access control
-   No traditional server required

## 🧰 Technology Stack

### Frontend

-   HTML5
-   CSS3
-   JavaScript
-   WebSocket API

### AWS

  -----------------------------------------------------------------------
  Service                             Purpose
  ----------------------------------- -----------------------------------
  **Amazon API Gateway**              Provides the WebSocket API and
                                      manages real-time connections

  **AWS Lambda**                      Executes serverless chat logic

  **Amazon DynamoDB**                 Stores application data

  **AWS IAM**                         Manages permissions and access
                                      control
  -----------------------------------------------------------------------

## 🔄 How It Works

### 1. User opens the application

The user opens the chat application in a web browser.

The frontend creates a WebSocket connection to the API Gateway WebSocket
API.

### 2. WebSocket connection is established

The browser connects to the deployed WebSocket endpoint.

Example:

``` text
wss://<api-id>.execute-api.<region>.amazonaws.com/<stage>
```

The frontend displays the connection status as **Connected** when the
WebSocket handshake succeeds.

### 3. User sends a message

When the user clicks **Send** or presses **Enter**, the frontend sends a
JSON payload through the WebSocket connection.

Example:

``` json
{
  "action": "sendMessage",
  "message": "Hello!",
  "username": "User",
  "timestamp": "2026-09-18T00:00:00.000Z"
}
```

### 4. API Gateway routes the request

Amazon API Gateway receives the WebSocket message and uses the
configured route to invoke the appropriate Lambda function.

The application uses the `sendMessage` action for chat messages.

### 5. Lambda processes the message

AWS Lambda handles the server-side chat logic.

Depending on the configured backend implementation, Lambda can:

-   Process the incoming message
-   Store data in DynamoDB
-   Retrieve connection information
-   Send a response/message through the WebSocket API

### 6. DynamoDB stores data

Amazon DynamoDB provides serverless NoSQL storage for the application.

It can be used to store information such as:

-   WebSocket connection IDs
-   User information
-   Chat messages
-   Timestamps

### 7. IAM secures service access

AWS IAM roles and policies provide Lambda with only the permissions it
needs to interact with other AWS resources.

This avoids placing AWS credentials directly inside the frontend
application.

## 🔌 WebSocket Events

A typical API Gateway WebSocket API can use routes such as:

  Route           Purpose
  --------------- -----------------------------------------
  `$connect`      Handles a new WebSocket connection
  `$disconnect`   Handles a disconnected client
  `sendMessage`   Processes an incoming chat message
  `$default`      Handles unmatched routes, if configured

The exact routes and Lambda integration depend on the deployed API
Gateway configuration.

## 🔐 Security

Security is handled using AWS IAM.

The frontend does **not** contain AWS access keys or secret credentials.

Instead:

``` text
Lambda
   │
   │ IAM Role
   ▼
AWS Resources
```

IAM policies determine which AWS resources and operations the Lambda
function can access.

## 📁 Project Structure

A simple project structure can look like this:

``` text
serverless-chat/
│
├── index.html
├── README.md
│
└── architecture/
    └── architecture-diagram.png
```

If the Lambda backend is maintained in the same repository, it can be
organized as:

``` text
serverless-chat/
│
├── frontend/
│   └── index.html
│
├── backend/
│   └── lambda/
│       └── handler.*
│
├── architecture/
│   └── architecture-diagram.png
│
└── README.md
```

## 🚀 Deployment Overview

### Prerequisites

Before deploying the project, make sure you have:

-   An AWS account
-   An AWS IAM user/role with appropriate permissions
-   An Amazon API Gateway WebSocket API
-   An AWS Lambda function
-   An Amazon DynamoDB table
-   An IAM execution role for Lambda
-   A web browser

### AWS Setup

The general deployment flow is:

``` text
1. Create DynamoDB table
        ↓
2. Create Lambda function
        ↓
3. Create IAM role/policies
        ↓
4. Create API Gateway WebSocket API
        ↓
5. Configure WebSocket routes
        ↓
6. Integrate routes with Lambda
        ↓
7. Deploy the API to a stage
        ↓
8. Add the WebSocket endpoint to the frontend
        ↓
9. Open the application and test messaging
```

### Configure the WebSocket Endpoint

Update the frontend WebSocket URL with your deployed API Gateway
endpoint:

``` javascript
const WS_URL =
  "wss://<api-id>.execute-api.<region>.amazonaws.com/<stage>";
```

For example:

``` javascript
const WS_URL =
  "wss://example.execute-api.us-east-1.amazonaws.com/prod";
```

Do not expose AWS secret access keys in the frontend.

## 🧪 Testing

After deployment, test the following:

### Connection

-   Open the application.
-   Confirm the status changes from `Connecting...` to `Connected`.

### Sending Messages

-   Enter a username.
-   Type a message.
-   Click **Send**.
-   Confirm the message is transmitted successfully.

### Reconnection

-   Temporarily interrupt the WebSocket connection.
-   Confirm the application detects the disconnection.
-   Confirm that the automatic reconnect mechanism attempts to
    reconnect.

### DynamoDB

Verify that the expected connection or message data is being stored
according to your Lambda implementation.

### IAM

Verify that the Lambda function can perform its required DynamoDB
operations without granting unnecessary permissions.

## 💡 Key Serverless Concepts Demonstrated

This project demonstrates several important AWS concepts:

### Serverless Architecture

There is no traditional application server to provision or maintain.

``` text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
DynamoDB
```

AWS manages the underlying infrastructure.

### WebSockets

Unlike traditional request/response HTTP communication, WebSockets
maintain a persistent connection that enables real-time communication
between the browser and backend.

### Event-Driven Processing

A WebSocket event received by API Gateway can trigger a Lambda function.

``` text
WebSocket Event
      ↓
API Gateway
      ↓
Lambda
      ↓
Application Logic
```

### NoSQL Database

DynamoDB provides a managed NoSQL database suitable for highly scalable
serverless applications.

### Least-Privilege Access

IAM policies can restrict Lambda to only the AWS resources and actions
required by the application.

## 📊 Benefits

-   **Real-time communication** through WebSockets
-   **Serverless** --- no servers to manage
-   **Automatic scalability** provided by managed AWS services
-   **Pay-per-use model** for serverless components
-   **Managed NoSQL storage** with DynamoDB
-   **Secure service-to-service access** through IAM
-   **Good foundation for extending the application** with additional
    features

## 🔮 Possible Future Improvements

The application can be extended with:

-   User authentication
-   Private chat rooms
-   Multiple chat rooms
-   Message history
-   Online/offline user presence
-   Message deletion
-   File and image sharing
-   Typing indicators
-   Read receipts
-   API authentication and authorization
-   Infrastructure as Code using Terraform
-   Monitoring and logging with Amazon CloudWatch
-   Custom domain and HTTPS frontend hosting

## 📸 Architecture Diagram

Add the project architecture diagram to the repository and reference it
here:

``` markdown
![Serverless Chat Architecture](./architecture/architecture-diagram.png)
```

## 🎯 Learning Objectives

This project was created to gain practical experience with:

-   AWS API Gateway WebSocket APIs
-   AWS Lambda
-   Amazon DynamoDB
-   AWS IAM
-   WebSocket communication
-   Event-driven serverless architecture
-   Real-time web applications
-   AWS service integration

## 👨‍💻 Author

**Luthira Peiris**

GitHub: `https://github.com/LuthiraPeiris`

Medium: `https://medium.com/@luthirapeiris1`

LinkedIn: `https://www.linkedin.com/in/luthirapeiris/`

------------------------------------------------------------------------

⭐ If you found this project useful, consider giving the repository a
star.

**Built with AWS Serverless Services**
