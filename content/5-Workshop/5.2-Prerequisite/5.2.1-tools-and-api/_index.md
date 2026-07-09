---
title : "Tools & API Key"
date : 2024-01-01 
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
- Download all source codes here: **[ [...] Source code download link will be updated later ]**

*(Note: After downloading, you will have zip files containing the `dist` folder for S3 and the `playwright` source code for AWS Lambda/Fargate).*

**3. Google Gemini API Key**

For the system to automatically analyze error logs via AI, you need to provide a Google API Key. Follow these steps to generate it:

**Step 1:** Go to the [Google AI Studio](https://aistudio.google.com/app/apikey) page and log in with your Google account.

![Access Google AI Studio](/images/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/1-access-google-ai-studio.png?featherlight=false&width=90pc)

**Step 2:** On the main interface, click the **Create API key** button.

![Create API Key](/images/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/2-create-api-key.png?featherlight=false&width=90pc)

**Step 3:** Copy the generated key string. Save this key in a safe place (like Notepad) to fill in the AWS Lambda environment variables configuration in the next practice sections.

![Copy API Key](/images/5-Workshop/5.2-Prerequisite/5.2.1-tools-and-api/3-copy-api-key.png?featherlight=false&width=90pc)
