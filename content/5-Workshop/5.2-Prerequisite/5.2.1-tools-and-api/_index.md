---
title : "Tools & API Key"
date : 2026-07-10
weight : 1
chapter : false
pre : " <b> 5.2.1. </b> "
---

#### Necessary Tools and Resources

Before building the Serverless Playwright system, you need to prepare the following resources:

**1. AWS Account**

- You need access to the AWS Management Console.
- It is recommended to use an account with Administrator Access to perform this Lab smoothly.

**2. Source Code**

- You need to download the pre-packaged source codes for the Frontend (UI) and Backend (processing).
- Download the source files below:

| File | Description |
| ------ | ------------- |
| [lambda-backend.zip](/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/lambda-backend.zip) | Main backend Lambda function |
| [lambda-coordinator.zip](/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/lambda-coordinator.zip) | Coordinator Lambda function |
| [lambda-error-handler.zip](/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/lambda-error-handler.zip) | Error handler Lambda function |
| [lambda-postprocessing.zip](/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/lambda-postprocessing.zip) | Post-processing Lambda function |
| [dist.zip](/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/dist.zip) | Frontend (UI) build for S3 |

> [!NOTE]
> **For `dist.zip` (Frontend):** After downloading, **extract** the zip file and upload **the contents inside** (not the zip itself) to your S3 WebUI bucket. The S3 bucket for the frontend will be created in **[5.2.3. Create S3](../5.2.4-create-s3/)**.
>
> **For the Lambda `.zip` files:** These are uploaded directly (without extracting) when deploying each Lambda function in the corresponding sections.

**3. Google Gemini API Key**

For the system to automatically analyze error logs via AI, you need to provide a Google API Key. Follow these steps to generate it:

**Step 1:** Go to the [Google AI Studio](https://aistudio.google.com/app/apikey) page and log in with your Google account.

![Access Google AI Studio](/images/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/1-access-google-ai-studio.png?featherlight=false&width=90pc)

**Step 2:** On the main interface, click the **Create API key** button.

![Create API Key](/images/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/2-create-api-key.png?featherlight=false&width=90pc)

**Step 3:** Copy the generated key string. Save this key in a safe place (like Notepad) to fill in the AWS Lambda environment variables configuration in the next practice sections.

![Copy API Key](/images/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/3-copy-api-key.png?featherlight=false&width=90pc)

---

The tools and API Key preparation is complete. Next, we will move on to **[5.2.2. Create VPC](../5.2.2-create-vpc/)** to build the private network infrastructure for the system.
