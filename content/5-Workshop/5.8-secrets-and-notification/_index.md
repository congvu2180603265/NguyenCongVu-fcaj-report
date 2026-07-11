---
title : "Secrets & Notification"
date : 2026-06-19
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---
#### Introduction
 
This section sets up 2 independent pieces that both serve the `playwright-postprocessing` Lambda: a secure place to store secret keys, and a channel for sending out test result notifications. It has 2 sub-sections:
 
**5.8.1 — Secrets Manager**
Create the `playwright/openai-api-key` secret in AWS Secrets Manager to store the AI API Key. Instead of hard-coding the key in the Lambda source code (risking exposure, hard to rotate), the Lambda automatically retrieves the key from Secrets Manager on every run, via the `OPENAI_SECRET_NAME` environment variable.
 
**5.8.2 — Notification (SES)**
Configure Amazon SES so the `playwright-postprocessing` Lambda automatically sends a test result report email after each run. This covers verifying the sender email, sending a test email, and an important note on the SES Sandbox mode limitation (can only send to verified emails) along with how to request Production Access when broader sending is needed.
 
See the detailed steps in the 2 sub-sections below.
 
