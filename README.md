# 🤖 AI Daily Study Planner using AWS Bedrock

An AI-powered daily study planner built using **AWS Lambda, Amazon Bedrock, and Amazon SES**. The system automatically generates a personalized and realistic study schedule based on the student's goals, the current date, and the day of the week, and delivers the schedule directly to the student's email.

---

## 📌 Project Overview

The **AI Daily Study Planner** is a serverless productivity application designed for students.

The application uses **Amazon Bedrock** to generate an intelligent daily schedule and **Amazon SES (Simple Email Service)** to send the generated schedule through email.

The planner considers:

* 📚 College Work
* 🐍 Python Practice
* ☁️ AWS Learning
* 🧠 Aptitude Preparation
* 🏋️ Exercise
* 🍱 Lunch
* 🍽️ Dinner
* ☕ Short Breaks
* 😌 Relaxation Time
* 🎯 Student Goals

The schedule automatically changes depending on the day:

* **Monday–Friday:** Normal study and productivity schedule
* **Saturday:** More time for projects and certifications
* **Sunday:** Weekly review and planning for the upcoming week

---

# 🏗️ System Architecture

```text
                    ┌──────────────────────────┐
                    │     AWS EventBridge      │
                    │   Scheduled Trigger      │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │       AWS Lambda         │
                    │                          │
                    │  Python Application      │
                    │                          │
                    │  • Get Date & Day        │
                    │  • Read Environment Vars │
                    │  • Create AI Prompt      │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │    Amazon Bedrock        │
                    │                          │
                    │     Nova Lite Model      │
                    │                          │
                    │  Generate Daily Schedule │
                    └────────────┬─────────────┘
                                 │
                                 │ AI Generated
                                 │ Study Schedule
                                 ▼
                    ┌──────────────────────────┐
                    │       AWS Lambda         │
                    │                          │
                    │ Receive AI Response      │
                    │ Format Email Content     │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │       Amazon SES         │
                    │                          │
                    │   Simple Email Service   │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │       Student Email      │
                    │                          │
                    │ 📅 Daily Study Planner   │
                    └──────────────────────────┘
```

---

# 🔄 Application Workflow

```text
1. EventBridge triggers Lambda
              ↓
2. Lambda starts execution
              ↓
3. Lambda gets current date and day
              ↓
4. Lambda reads student information
   and goals from environment variables
              ↓
5. Lambda creates AI prompt
              ↓
6. Prompt is sent to Amazon Bedrock
              ↓
7. Nova Lite generates study schedule
              ↓
8. Lambda receives AI response
              ↓
9. Lambda sends schedule using Amazon SES
              ↓
10. Student receives email
```

---

# ☁️ AWS Services Used

## 1. AWS Lambda

AWS Lambda is the main compute service used in this project.

The Lambda function:

* Executes the Python code
* Gets the current date and day
* Reads configuration from environment variables
* Creates the AI prompt
* Communicates with Amazon Bedrock
* Receives the generated schedule
* Sends the schedule through Amazon SES

The application is **serverless**, so there is no need to manage a dedicated server.

---

## 2. Amazon Bedrock

Amazon Bedrock provides access to foundation models.

In this project, **Amazon Nova Lite** is used to generate the personalized study schedule.

The Lambda function sends a prompt containing:

```text
Current Date
Day
Student Name
Student Goals
Scheduling Requirements
Special Weekend Rules
```

Bedrock then generates a structured daily planner.

---

## 3. Amazon SES

**Amazon Simple Email Service (SES)** is used to send the AI-generated schedule to the student's email address.

The email contains:

```text
Subject:
📅 Daily Study Planner - [Date]

Body:
AI-generated daily schedule
```

---

## 4. Amazon EventBridge Scheduler

EventBridge Scheduler can be used to automatically trigger the Lambda function every day.

For example:

```text
Every day at 6:00 AM
        ↓
EventBridge
        ↓
Lambda
        ↓
Bedrock
        ↓
SES
        ↓
Email
```

This makes the entire system automatic.

---

# 🧠 AI Prompt Design

The Lambda function dynamically creates the prompt using Python.

The prompt contains information such as:

```text
Today's Date
Day
Student Name
Student Goals
```

The AI is instructed to:

* Start the schedule at 6:00 AM
* Finish before 10:00 PM
* Include study sessions
* Include exercise
* Include meals
* Include breaks
* Include relaxation
* Provide a productivity tip
* Provide a motivational quote

### Weekend Intelligence

The prompt also contains conditional planning rules.

### Saturday

The AI gives additional time for:

* Projects
* Certifications
* Skill development

### Sunday

The AI focuses on:

* Weekly review
* Previous-week reflection
* Next week's goals
* Planning

---

# 🧩 Project Components

```text
AI-Daily-Study-Planner/
│
├── lambda_function.py
│
├── README.md
│
└── requirements.txt
```

### `lambda_function.py`

Contains the main AWS Lambda application.

It handles:

* AWS service connections
* Environment variables
* Date and time
* Prompt creation
* Bedrock API call
* AI response processing
* SES email delivery
* Error handling

### `README.md`

Project documentation and architecture.

### `requirements.txt`

Python dependencies required when deploying outside the Lambda runtime or packaging dependencies.

---

# 🔐 Environment Variables

The application uses environment variables instead of hard-coding configuration values.

Required variables:

| Variable       | Description                       |
| -------------- | --------------------------------- |
| `MODEL_ID`     | Amazon Bedrock model ID           |
| `STUDENT_NAME` | Student's name                    |
| `GOALS`        | Student's personal/academic goals |
| `FROM_EMAIL`   | Verified SES sender email         |
| `TO_EMAIL`     | Destination email                 |

Example:

```text
MODEL_ID=your-bedrock-model-id
STUDENT_NAME=Your Name
GOALS=Learn Python, AWS and prepare for placements
FROM_EMAIL=verified-sender@example.com
TO_EMAIL=student@example.com
```

> ⚠️ Do not commit sensitive credentials, API keys, passwords, or private configuration files to GitHub.

---

# 🔑 IAM Permissions

The Lambda execution role needs permission to communicate with the required AWS services.

Typical permissions include:

```text
bedrock:Converse
ses:SendEmail
```

The Lambda function should also have the standard CloudWatch Logs permissions:

```text
logs:CreateLogGroup
logs:CreateLogStream
logs:PutLogEvents
```

For production environments, follow the **principle of least privilege** and grant only the permissions required by the application.

---

# 📧 Amazon SES Configuration

Before sending emails using SES:

1. Open Amazon SES.
2. Verify the sender email address.
3. Verify the recipient email address if your SES account is still in the sandbox.
4. Configure the Lambda IAM role with SES permissions.
5. Set the verified email addresses as Lambda environment variables.

---

# 🤖 Amazon Bedrock Configuration

Before running the Lambda function:

1. Open Amazon Bedrock in your AWS account.
2. Select the AWS region where the Lambda will run.
3. Make sure the required model is available.
4. Enable/access the required foundation model according to your AWS account configuration.
5. Set the correct model ID in:

```text
MODEL_ID
```

---

# ⚙️ Lambda Configuration

Recommended basic configuration:

```text
Runtime:
Python 3.x

Architecture:
x86_64 or arm64

Region:
us-east-1

Handler:
lambda_function.lambda_handler
```

Environment variables:

```text
MODEL_ID
STUDENT_NAME
GOALS
FROM_EMAIL
TO_EMAIL
```

---

# ⏰ Automation with EventBridge

To make the planner completely automatic, create an EventBridge Scheduler rule.

Example:

```text
Schedule:
Every day at 6:00 AM

Target:
AWS Lambda

Target Function:
AI Daily Study Planner
```

Architecture:

```text
        Every Day
           │
           ▼
┌────────────────────┐
│ EventBridge        │
│ Scheduler          │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ AWS Lambda         │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Amazon Bedrock     │
│ Nova Lite          │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Amazon SES         │
└─────────┬──────────┘
          │
          ▼
      📧 Email
```

---

# 🧪 Example AI Output

The AI may generate a schedule similar to:

```text
📅 Daily Study Planner

6:00 AM – 6:30 AM
Exercise

6:30 AM – 7:00 AM
Breakfast & Preparation

7:00 AM – 9:00 AM
College Work

9:00 AM – 9:15 AM
Short Break

9:15 AM – 10:15 AM
Python Practice

10:15 AM – 10:30 AM
Break

10:30 AM – 11:30 AM
AWS Learning

12:30 PM – 1:15 PM
Lunch

2:00 PM – 3:00 PM
College Assignments

4:00 PM – 5:00 PM
Aptitude Preparation

6:00 PM – 7:00 PM
Relaxation

7:30 PM – 8:15 PM
Dinner

8:15 PM – 9:15 PM
Revision

9:15 PM – 9:30 PM
Plan Tomorrow

Productivity Tip:
Focus on one important task at a time.

Motivational Quote:
Small consistent efforts lead to big results.
```

> The actual schedule is dynamically generated by Amazon Bedrock and may differ each day.

---

# 🛡️ Error Handling

The Lambda function uses a `try-except` block to handle runtime errors.

```python
try:
    # AI generation
    # Email sending

except Exception as e:
    print("Error:", str(e))
```

If an error occurs, the Lambda function returns:

```text
HTTP Status Code: 500
```

The error is also printed to the Lambda logs.

---

# 📊 Monitoring

AWS CloudWatch Logs can be used to monitor the Lambda function.

The application logs:

```text
===== AI Generated Schedule =====

Email sent successfully.
```

Errors are logged using:

```text
Error: <error message>
```

This makes debugging easier when Bedrock or SES requests fail.

---

# 💡 Key Features

* 🤖 AI-generated personalized schedules
* ☁️ Fully serverless AWS architecture
* 📅 Automatic date and day detection
* 🧠 Amazon Bedrock Nova Lite integration
* 📧 Automated email delivery using Amazon SES
* 🎯 Goal-based study planning
* 🏋️ Exercise and relaxation included
* 📚 Academic task management
* 🐍 Python learning sessions
* ☁️ AWS learning sessions
* 🧠 Aptitude preparation
* 📆 Weekend-specific planning
* 🔐 Environment-variable based configuration
* 📊 CloudWatch logging
* ⏰ Can be automated using EventBridge Scheduler

---

# 🚀 Future Enhancements

The project can be extended with:

* 📱 Mobile application
* 🌐 Web dashboard
* 📊 Productivity analytics
* 🗄️ Amazon DynamoDB for storing schedules
* 🔔 Amazon SNS notifications
* 📈 Weekly productivity reports
* 🎯 Progress tracking
* 🧠 AI-based difficulty adjustment
* 📅 Google Calendar integration
* 🗣️ Voice-based planning
* 🌙 Personalized sleep schedule
* 🔄 Automatic schedule adjustment based on completed tasks

---

# 🎯 Learning Outcomes

Through this project, the following technologies and concepts are demonstrated:

### AWS

* AWS Lambda
* Amazon Bedrock
* Amazon SES
* Amazon EventBridge
* AWS IAM
* Amazon CloudWatch

### Programming

* Python
* `boto3`
* Environment variables
* Exception handling
* API/service integration

### AI

* Generative AI
* Prompt engineering
* Foundation models
* AI-powered personalization

### Cloud Architecture

* Serverless computing
* Event-driven architecture
* Managed AI services
* Cloud-based email automation

---

# 🏆 Project Architecture Summary

```text
             ┌───────────────────┐
             │ EventBridge       │
             │ Scheduler         │
             └─────────┬─────────┘
                       │
                       ▼
             ┌───────────────────┐
             │ AWS Lambda        │
             │ Python            │
             └─────────┬─────────┘
                       │
             ┌─────────▼─────────┐
             │ Amazon Bedrock    │
             │ Nova Lite         │
             └─────────┬─────────┘
                       │
                       ▼
             ┌───────────────────┐
             │ AI Study Schedule │
             └─────────┬─────────┘
                       │
                       ▼
             ┌───────────────────┐
             │ Amazon SES        │
             │ Email Service     │
             └─────────┬─────────┘
                       │
                       ▼
             ┌───────────────────┐
             │ Student Email     │
             └───────────────────┘
```

---

# 👨‍💻 Author

**Sriram**

Electronics and Communication Engineering (ECE)

Interested in:

* ☁️ Cloud Computing
* 🤖 Generative AI
* 🐍 Python
* 💻 Software Development
* 🚀 AWS

---

# 📜 License

This project is created for **educational and learning purposes**.
