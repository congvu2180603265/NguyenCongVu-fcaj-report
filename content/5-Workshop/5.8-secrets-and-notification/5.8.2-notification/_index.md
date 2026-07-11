---
title : "SES Email Notification"
date : 2026-06-19
weight : 2
chapter : false
pre : " <b> 5.8.2 </b> "
---
#### Part 1: Verify the Sender Email in SES
 
**Step 1:** Go to the **Amazon SES Console** (make sure you're in the `ap-southeast-1` region).
 
**Step 2:** In the left-hand menu → **Verified identities** → **Create identity**.
 
**Step 3:** Identity type: select **Email address**.
 
**Step 4:** Enter the email address used to send reports (this email will be the `SES_SENDER_EMAIL` environment variable for the Post-processing Lambda).
 
**Step 5:** Click **Create identity**.
 
![Create a Verified Identity in SES](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/1-create-identity.png?featherlight=false&width=90pc)
 
**Step 6:** AWS sends a verification email to the address you just entered → open your inbox, click the **Verify** link in that email.
 
**Step 7:** Go back to the SES Console, refresh — the identity status should switch from **Pending verification** to **Verified**.
 
![Identity switched to Verified](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/2-identity-verified.png?featherlight=false&width=90pc)
 
---
 
#### Part 2: Send a Test Email from the SES Console
 
**Step 1:** Go to **Verified identities**, click on the email you just verified.
 
**Step 2:** Click the **Send test email** tab (or the **Send test email** button at the top).
 
**Step 3:** From-address: select the verified email.
 
**Step 4:** Scenario: select **Custom** (or leave at default).
 
**Step 5:** To-address: another verified email (since you're in Sandbox mode, see the note below).
 
**Step 6:** Subject/Body: enter any test content, e.g. "Test SES Playwright System".
 
**Step 7:** Click **Send test email**.

![Fill in the test email form](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/4-Send-test-email.png?featherlight=false&width=90pc)

**Step 8:** Check the To-address inbox — the email should have arrived.

![Fill in the test email form](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/5-mail.png?featherlight=false&width=90pc)
 
---
 
{{% notice warning %}}
**Important note: SES Sandbox mode**
 
SES is in Sandbox mode by default, which means:
- You can only send to verified email addresses (including test emails)
- The daily sending limit is very low
How to handle it:
- If the demo only needs to send to a few fixed people on the team → verify those emails in Part 1 (repeat the verification process for each report recipient)
- If you need to send to arbitrary emails (real production use) → you must request Production Access:
  1. SES Console → Account dashboard → the Sending statistics section will show a "Your account is in the sandbox" banner
  2. Click **Request production access**
  3. Fill out the form: describe the use case (e.g. "Internal company system for sending automated test result reports"), mail type (Transactional), estimated daily sending volume
  4. Submit — AWS usually responds within 24h. Do this step early, don't wait until right before the demo to request it.
{{% /notice %}}

---

Next, we will move on to **[5.9. Auth & API Gateway](../../5.9-api-gateway-and-auth/)** to set up Cognito authentication and the API layer.
