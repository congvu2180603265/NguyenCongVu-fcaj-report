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

**Step 2:** On the **User pools** list screen, click the orange **Create user pool** button. AWS will redirect you to the **Set up resources for your application** page with 3 steps.

**Step 3:** Under **Define your application**:
- **Application type**: Select **Single-page application (SPA)**.
- **Name your application**: Enter `playwright-app`.

![Configure Define your application - select SPA and name playwright-app](/images/5-Workshop/5.9-api-gateway-and-auth/1.1-cognito-define-app.png?featherlight=false&width=90pc)

**Step 4:** Under **Configure options**:
- **Options for sign-in identifiers**: Tick **Email**.
- **Required attributes for sign-up**: Tick **email**.

**Step 5:** Under **Add a return URL - optional**:
- **Return URL**: Enter `http://localhost:3000` (this will be updated to the CloudFront Domain Name after completing section 5.10).

![Configure options and Return URL](/images/5-Workshop/5.9-api-gateway-and-auth/1.2-cognito-configure-options.png?featherlight=false&width=90pc)

**Step 6:** Click the **Create user directory** button at the bottom to finalize the creation.

![User pool playwright-user-pool created](/images/5-Workshop/5.9-api-gateway-and-auth/1-user-pool-created.png?featherlight=false&width=90pc)

**Step 7:** Once successfully created, click on the newly created User Pool name `playwright-user-pool` in the list.

**Step 8:** On the User Pool overview page, copy and note down the **User Pool ID** (format example: `ap-southeast-1_xxxxxxxxx`).
![playwright-user-pool Overview page, copy User Pool ID](/images/5-Workshop/5.9-api-gateway-and-auth/1.8-user-pool-overview.png?featherlight=false&width=90pc)

**Step 9:** Go to **Applications** → **App clients** in the left-hand menu, click on `playwright-app`.

**Step 10:** Copy and note down the **Client ID** (a random alphanumeric string).
![App client playwright-app detail page, copy Client ID](/images/5-Workshop/5.9-api-gateway-and-auth/1.10-app-client-detail.png?featherlight=false&width=90pc)

{{% notice warning %}}
Don't forget to navigate back to the **AWS Lambda** console, open the `playwright-api-backend` function (created in Section 5.7). Go to the **Configuration -> Environment variables** section, click **Edit**, and paste the actual ID into the `COGNITO_USER_POOL_ID` variable. If you skip this step, your API Backend will return a 401 authentication error.
{{% /notice %}}

---

#### Part 2: Create Users (Test Accounts)

*(Section 5.10 requires using 3 test accounts to log in)*

**Step 1:** Go back to the `playwright-user-pool` User Pool interface, select the **Users** tab.

**Step 2:** Click the **Create user** button.

**Step 3:** Configure the first account (Admin):
- Select **Send an email invitation**.
- Enter **Email address** (use your real email to receive the password, or any virtual email).
- Password section: Select **Generate a password** or set a standard password yourself.
- Click **Create user**.
![Enter info to create Admin-test user](/images/5-Workshop/5.9-api-gateway-and-auth/3.4-create-user-admin-test.png?featherlight=false&width=90pc)

**Step 4:** Repeat Step 2 and Step 3 to create 2 more accounts (e.g., QA and Developer). Once created, you will see 3 accounts in the **Force change password** state (requiring a password change on first login).
![3 test accounts created](/images/5-Workshop/5.9-api-gateway-and-auth/3-test-users-created.png?featherlight=false&width=90pc)

---

#### Part 3: Create API Gateway and Configure Cognito Authorizer

**Step 1:** In the AWS Console search bar, type `API Gateway` and select **API Gateway**.

**Step 2:** Find the **HTTP API** box (most commonly used for modern Serverless/Lambda apps) and click **Build**.
![Select HTTP API to Build](/images/5-Workshop/5.9-api-gateway-and-auth/5.3-api-gateway-http-build.png?featherlight=false&width=90pc)

**Step 3:** Name the API `playwright-api`. Click **Next** until the Review screen, then click **Create**.
![Create API playwright-api](/images/5-Workshop/5.9-api-gateway-and-auth/5.5-api-integration-lambda.png?featherlight=false&width=90pc)

![API playwright-api created successfully](/images/5-Workshop/5.9-api-gateway-and-auth/7b-api-created.png?featherlight=false&width=90pc)

**Step 4:** Configure CORS (To allow the Frontend on a different domain to call the API):
- In the left-hand menu, select **CORS**.
- Configure **Access-Control-Allow-Origin**: Enter `*` (or your future CloudFront domain) then click **Add**.
- Configure **Access-Control-Allow-Headers**: Enter `*` or necessary headers (like `Authorization`, `Content-Type`).
- Configure **Access-Control-Allow-Methods**: Select `*` (or GET, POST, PUT, DELETE, OPTIONS).
- Click **Save**.

![Configure CORS in API Gateway](/images/5-Workshop/5.9-api-gateway-and-auth/3.4.CORS.png?featherlight=false&width=90pc)

**Step 5:** Add Routes and Integration (Connect to Lambda):
- In the left-hand menu, select **Routes** -> Click **Create**.
- Add the corresponding Routes required by the Frontend:
  - **Test**: `POST /trigger` · `GET /test-runs` · `GET /test-runs/{id}` · `GET /stats` · `GET /test-suites`
  - **Schedule**: `GET /schedules` · `POST /schedules` · `DELETE /schedules/{id}`
  - **Config & Users**: `GET /email-config` · `POST /email-config` · `GET /users` · `GET /audit-logs`
  - **Report**: `GET /reports` · `GET /AI-insights`
- Switch to the **Integrations** menu, select each Route you just created -> Click **Attach integration** -> Select **AWS Lambda** -> Select the corresponding Lambda functions created in section 5.7 -> Click **Create**.
  
![List of created Routes in API Gateway](/images/5-Workshop/5.9-api-gateway-and-auth/3.5.Routes.png?featherlight=false&width=90pc)

**Step 6:** Configure Cognito Authorizer (API Security):

**Go to Authorization:**
- In the left-hand menu under **Develop**, select **Authorization**.

**Create Authorizer:**
- Switch to the **Manage authorizers** tab (next to Attach authorizers to routes) → Click **Create**.
- Fill in the configuration:
  - **Authorizer type**: Select `JWT`.
  - **Name**: Enter `cognito-authorizer`.
  - **Identity source**: Enter `$request.header.Authorization` (AWS auto-fills this).
  - **Issuer URL**: Enter `https://cognito-idp.<region>.amazonaws.com/<User_Pool_ID>`
  - **Audience**: Click **Add audience** and enter the App Client ID obtained in Part 1.
![Authorization section in the left menu](/images/5-Workshop/5.9-api-gateway-and-auth/2.6%20taoAPIbaomat.png?featherlight=false&width=90pc)

- Click **Create** to complete.

![Authorizer CognitoAuth created](/images/5-Workshop/5.9-api-gateway-and-auth/8a-authorizer-created.png?featherlight=false&width=90pc)

**Attach Authorizer to Routes:**
- Switch back to the **Attach authorizers to routes** tab.
- Select the Routes to secure (e.g., `/trigger`, `/test-runs`, ...).
- In the **Select authorizer** box, select the newly created `cognito-authorizer` → Click **Attach authorizer**.

![Route with JWT Auth attached](/images/5-Workshop/5.9-api-gateway-and-auth/8b-route-jwt-attached.png?featherlight=false&width=90pc)

**Step 7:** Get Invoke URL:
- Return to the HTTP API overview page (**Stages** section -> select the `$default` or `prod` stage).
- You will see an **Invoke URL** formatted like: `https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com`.
- Copy this URL. This is the `API_BASE_URL` to be pasted into the `config.js` file in Part 1 - Step 2 of Section 5.10.
![Stage $default with Invoke URL](/images/5-Workshop/5.9-api-gateway-and-auth/9-stage-invoke-url.png?featherlight=false&width=90pc)

---

#### Summary of Results

At this point, you have the 3 critical values needed for section 5.10:
- **API_BASE_URL** (Invoke URL)
- **userPoolId** (Cognito User Pool ID)
- **clientId** (Cognito App Client ID)

---

Next, we will move on to **[5.10. Frontend](../5.10-frontend/)** to deploy the Dashboard UI to S3 and CloudFront.
