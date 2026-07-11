---
title : "Auth & API Gateway"
date : 2026-06-20
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Introduction

Set up sign-in, user authorization, and an API gateway for the Frontend to communicate with the Backend.


---

#### Part 1: Create a Cognito User Pool

**Step 1:** Sign in to the **AWS Management Console**, search for `Cognito` in the search bar, select **Amazon Cognito**.

**Step 2:** Click the orange **Create user pool** button.

**Step 3:** On the **Configure sign-in experience** page: keep Provider types as **Cognito user pool**; under Sign-in options, tick **Email**. Click **Next**.
![alt text](/images/5-Workshop/5.9-api-gateway-and-auth/1.3.png?featherlight=false&width=90pc)
**Step 4:** On the **Configure security requirements** page: under Password policy, select **Cognito defaults**; under Multi-factor authentication, select **No MFA**. Click **Next**.

**Step 5:** On the **Configure sign-up experience** page: under Required attributes for sign-up, tick **email**. Leave the rest at default. Click **Next**.

**Step 6:** On the **Configure message delivery** page: keep the default (Send email with Cognito). Click **Next**.

**Step 7:** On the **Integrate your app** page:
- User pool name: enter `playwright-user-pool`
- Under Initial app client → App type: select **Public client**
- App client name: enter `playwright-app`

**Step 8:** Scroll down to **Hosted authentication pages** (if present) or leave at default, don't enable it.

**Step 9:** Under **Callback URL(s)**: temporarily enter `http://localhost:3000` (this will be updated to the CloudFront Domain Name later, after completing section 5.10).

**Step 10:** Click **Next**, review the full configuration on the **Review and create** page, click **Create user pool**.

![User pool playwright-user-pool created](/images/5-Workshop/5.9-api-gateway-and-auth/1-user-pool-created.png?featherlight=false&width=90pc)

---

#### Part 2: Create Groups

**Step 1:** From the User Pool you just created (`playwright-user-pool`), in the left-hand menu, open **User management**.

**Step 2:** Click **Groups**.

**Step 3:** Click **Create group**.

**Step 4:** Enter Group name = `Admin`, leave the other fields blank, click **Create group**.
![alt text](/images/5-Workshop/5.9-api-gateway-and-auth/2.4.png?featherlight=false&width=90pc)

**Step 5:** Click **Create group** again, enter Group name = `Developer`, click **Create group**.

**Step 6:** Click **Create group** again, enter Group name = `QA`, click **Create group**.

![3 groups Admin, Developer, QA created](/images/5-Workshop/5.9-api-gateway-and-auth/2-groups-created.png?featherlight=false&width=90pc)

**Step 7:** Still in the left-hand menu, open **Applications** → click **App clients**.

**Step 8:** Confirm the App Client `playwright-app` was already created in Step 7 of Part 1 (Cognito creates it automatically when the User Pool is created). If it's missing, click **Create app client** to add it.

---

#### Part 3: Create Test Accounts

**Step 1:** In the left-hand menu, open **User management** → click **Users**.

**Step 2:** Click **Create user**.

**Step 3:** Under Invitation message: select **Don't send an invitation**.

**Step 4:** Enter Email address = `Admin-test@gmail.com`, tick **Mark email address as verified** if you want to skip the email verification step (not required for internal test accounts).
![alt text](/images/5-Workshop/5.9-api-gateway-and-auth/3.4.png?featherlight=false&width=90pc)
**Step 5:** Set a Temporary password (or let Cognito generate one), click **Create user**.

**Step 6:** Repeat Steps 2-5 to create user `Dev-test@gmail.com`.

**Step 7:** Repeat Steps 2-5 to create user `Qa-test@gmail.com`.

![3 test accounts created, Enabled status](/images/5-Workshop/5.9-api-gateway-and-auth/3-test-users-created.png?featherlight=false&width=90pc)

---

#### Part 4: Assign Test Accounts to Groups

**Step 1:** From the **Users** list, click on user `Admin-test@gmail.com`.

**Step 2:** Scroll down to **Group memberships**, click **Add user to group**.

**Step 3:** Tick Group **Admin**, click **Add**.

![User Admin-test assigned to the Admin group](/images/5-Workshop/5.9-api-gateway-and-auth/4-user-admin-added.png?featherlight=false&width=90pc)

**Step 4:** Go back to the **Users** list, click on user `Dev-test@gmail.com`.

**Step 5:** Scroll down to **Group memberships**, click **Add user to group**, tick Group **Developer**, click **Add**.

![User Dev-test assigned to the Developer group](/images/5-Workshop/5.9-api-gateway-and-auth/5-user-developer-added.png?featherlight=false&width=90pc)

**Step 6:** Go back to the **Users** list, click on user `Qa-test@gmail.com`.

**Step 7:** Scroll down to **Group memberships**, click **Add user to group**, tick Group **QA**, click **Add**.

![User Qa-test assigned to the QA group](/images/5-Workshop/5.9-api-gateway-and-auth/6-user-qa-added.png?featherlight=false&width=90pc)

---

#### Part 5: Create an API Gateway

**Step 1:** In the AWS Console search bar, search for `API Gateway`, select **API Gateway**.

**Step 2:** In the left-hand menu, click **APIs** → click the orange **Create API** button.

**Step 3:** Under **HTTP API**, click **Build** (don't choose REST API — HTTP API is simpler and sufficient for this workshop).
![alt text](/images/5-Workshop/5.9-api-gateway-and-auth/5.3.png?featherlight=false&width=90pc)
**Step 4:** On the **Create and configure integrations** page: click **Add integration** → select **Lambda**.

**Step 5:** In the Lambda function dropdown, select `playwright-api-backend`. Set **API name** = `playwright-api`. Click **Next**.
![alt text](/images/5-Workshop/5.9-api-gateway-and-auth/Screenshot%202026-07-10%20220935.png?featherlight=false&width=90pc)
**Step 6:** On the **Configure routes** page (optional): select Method **POST**, enter Resource path `/trigger`, leave the Integration target as `playwright-api-backend (Lambda)`. Click **Next**.

**Step 7:** On the **Define stages** page (optional): keep the default Stage name = `$default`, Auto-deploy = **enabled**. Click **Next**.
![alt text](/images/5-Workshop/5.9-api-gateway-and-auth/5.7.png?featherlight=false&width=90pc)
**Step 8:** On the **Review and create** page, verify all 3 sections: **API name and integrations** (playwright-api, playwright-api-backend), **Routes** (POST /trigger → playwright-api-backend), **Stages** ($default, Auto-deploy: enabled). Click **Create**.

![Review and create before creating the API](/images/5-Workshop/5.9-api-gateway-and-auth/7a-review-create-api.png?featherlight=false&width=90pc)

**Step 9:** Confirm the message **"Successfully created API playwright-api (8bsb7jbhu7)"** — the auto-generated API ID will differ for you.

![API playwright-api created successfully](/images/5-Workshop/5.9-api-gateway-and-auth/7b-api-created.png?featherlight=false&width=90pc)

---

#### Part 6: Create an Authorizer and Attach It to the Route

**Step 1:** Go into the `playwright-api` API you just created, in the left-hand menu (under **Develop**) click **Authorization**.

**Step 2:** On the **Manage authorizers** tab, click **Create**.

**Step 3:** Authorizer type: select **JWT**.

**Step 4:** Name: enter `cognito-authorizer`.

**Step 5:** Identity source: enter `$request.header.Authorization`.

**Step 6:** Issuer URL: enter `https://cognito-idp.ap-southeast-1.amazonaws.com/<user-pool-id>` (replace `<user-pool-id>` with the real ID, e.g. `ap-southeast-1_DXMd3Q6ee` — found in Cognito Console → User pool → Overview).

**Step 7:** Audience: enter the App Client name or ID, e.g. `playwright-app`.

**Step 8:** Click **Create**, confirm the message **"Successfully created authorizer 'cognito-authorizer'"**.

![Authorizer cognito-authorizer created](/images/5-Workshop/5.9-api-gateway-and-auth/8a-authorizer-created.png?featherlight=false&width=90pc)

**Step 9:** Switch to the **Attach authorizers to routes** tab.

**Step 10:** In the **Routes for playwright-api** panel, open `/trigger`, select route **POST**.

**Step 11:** In the right-hand panel **Authorizer for route POST /trigger**, select `cognito-authorizer`, confirm the route shows a green **JWT Auth** badge.

![Route POST /trigger with JWT Auth attached](/images/5-Workshop/5.9-api-gateway-and-auth/8b-route-jwt-attached.png?featherlight=false&width=90pc)

*(Note: The right-hand panel will show the Identity source, Issuer, and Audience you just configured — useful for a quick check if you hit a 401 error later on.)*

---

#### Part 7: Deploy the API and Get the Invoke URL

**Step 1:** In the left-hand menu (under **Deploy**), click **Stages**.

**Step 2:** Click the orange **Deploy** button in the top-right corner, select stage `$default`, click **Deploy**.

**Step 3:** In the **Stage details** panel, copy the **Invoke URL**, e.g. in the form `https://8bsb7jbhu7.execute-api.ap-southeast-1.amazonaws.com` — this will be used in section 5.10 (Frontend).

![Stage $default with the Invoke URL](/images/5-Workshop/5.9-api-gateway-and-auth/9-stage-invoke-url.png?featherlight=false&width=90pc)

---

#### Verification

- Test the API with Postman or curl, including a JWT token:
```bash
curl -X POST <invoke-url>/trigger -H "Authorization: Bearer <JWT>" -d '{"target_url":"https://example.com"}'
```
→ should receive a correct response, no 401/403 errors.
- If you get a 401 error even with a valid token, double-check the two Authorizer values: **Issuer** must be in the form `https://cognito-idp.ap-southeast-1.amazonaws.com/<user-pool-id>`, and **Audience** must match the correct App Client — either one being wrong will always return 401.

---

Next, we will move on to **[5.10. Frontend](../5.10-frontend/)** to deploy the Dashboard UI to S3 and CloudFront.
