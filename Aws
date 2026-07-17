import os
import boto3
from datetime import datetime

# AWS Clients
bedrock = boto3.client(
    "bedrock-runtime",
    region_name="us-east-1"
)

ses = boto3.client(
    "ses",
    region_name="us-east-1"
)

# Environment Variables
MODEL_ID = os.environ["MODEL_ID"]
STUDENT_NAME = os.environ["STUDENT_NAME"]
GOALS = os.environ["GOALS"]
FROM_EMAIL = os.environ["FROM_EMAIL"]
TO_EMAIL = os.environ["TO_EMAIL"]


def lambda_handler(event, context):

    # Get current date and day
    now = datetime.now()
    current_date = now.strftime("%d %B %Y")
    day_name = now.strftime("%A")

    # AI Prompt
    prompt = f"""
You are an intelligent AI Daily Planner and Productivity Coach.

Today's Date:
{current_date}

Day:
{day_name}

Student Name:
{STUDENT_NAME}

Student Goals:
{GOALS}

Create a realistic and balanced study schedule for today.

Instructions:

- Start the schedule at 6:00 AM.
- Finish before 10:00 PM.
- Include:
    • Exercise
    • College Work
    • Python Practice
    • AWS Learning
    • Aptitude Preparation
    • Lunch
    • Dinner
    • Short Breaks
    • Relaxation Time

Special Rules:

- If today is Saturday, allocate more time for projects and certifications.
- If today is Sunday, create a weekly review and next week's goals.
- Include one productivity tip.
- Include one motivational quote.
- Keep the schedule practical and achievable.
- Format the response neatly using headings and bullet points.
"""

    try:

        # Call Amazon Bedrock Nova Lite
        response = bedrock.converse(
            modelId=MODEL_ID,
            messages=[
                {
                    "role": "user",
                    "content": [
                        {
                            "text": prompt
                        }
                    ]
                }
            ]
        )

        ai_schedule = response["output"]["message"]["content"][0]["text"]

        print("===== AI Generated Schedule =====")
        print(ai_schedule)

        # Send Email
        ses.send_email(
            Source=FROM_EMAIL,
            Destination={
                "ToAddresses": [
                    TO_EMAIL
                ]
            },
            Message={
                "Subject": {
                    "Data": f"📅 Daily Study Planner - {current_date}"
                },
                "Body": {
                    "Text": {
                        "Data": ai_schedule
                    }
                }
            }
        )

        print("Email sent successfully.")

        return {
            "statusCode": 200,
            "body": {
                "message": "Daily planner generated and email sent successfully.",
                "date": current_date,
                "day": day_name,
                "schedule": ai_schedule
            }
        }

    except Exception as e:

        print("Error:", str(e))

        return {
            "statusCode": 500,
            "body": {
                "error": str(e)
            }
        }
