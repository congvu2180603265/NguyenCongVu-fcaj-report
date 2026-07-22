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

**Step 2:** On the **User pools** list screen, click the orange **Create user pool** button. AWS will take you to the **Set up resources for your application** page with 3 steps.

**Step 3:** Under **Define your application**:
- **Application type**: Select **Single-page application (SPA)**.
- **Name your application**: Enter `playwright-app`.

![Configure Define your application - select SPA and name it playwright-app](/images/5-Workshop/5.9-api-gateway-and-auth/1.1-cognito-define-app.png?featherlight=false&width=90pc)

**Step 4:** Under **Configure options**:
- **Options for sign-in identifiers**: Tick **Email**.
- **Required attributes for sign-up**: Tick **email**.

**Step 5:** Under **Add a return URL - optional**:
- **Return URL**: Enter `http://localhost:3000` (this will be updated to the CloudFront Domain Name later, after completing section 5.10).

![Configure Configure options and Return URL](/images/5-Workshop/5.9-api-gateway-and-auth/1.2-cognito-configure-options.png?featherlight=false&width=90pc)

**Step 6:** Click the **Create user directory** button at the bottom to finish.

![User pool playwright-user-pool created](/images/5-Workshop/5.9-api-gateway-and-auth/1-user-pool-created.png?featherlight=false&width=90pc)

**Step 7:** Once created, click the `playwright-user-pool` name in the list.

**Step 8:** On the User Pool overview page, copy and note down the **User Pool ID** (in the form `ap-southeast-1_xxxxxxxxx`).
![Overview page of playwright-user-pool, copy the User Pool ID](/images/5-Workshop/5.9-api-gateway-and-auth/1.8-user-pool-overview.png?featherlight=false&width=90pc)

**Step 9:** Go to **Applications** → **App clients** in the left-hand menu, click `playwright-app`.

**Step 10:** Copy and note down the **Client ID** (a random string).
![App client detail page for playwright-app, copy the Client ID](/images/5-Workshop/5.9-api-gateway-and-auth/1.10-app-client-detail.png?featherlight=false&width=90pc)

{{% notice warning %}}
Don't forget to go back to **AWS Lambda**, find the `playwright-api-backend` function (created in section 5.7). Go to **Configuration → Environment variables**, click **Edit**, and paste the real ID into the `COGNITO_USER_POOL_ID` variable. If you skip this step, the API Backend will return a 401 authentication error.
{{% /notice %}}

---

#### Part 2: Create Users (Test Accounts)

*(Section 5.10 requires 3 test accounts to log in with)*

**Step 1:** Go back to the `playwright-user-pool` interface, select the **Users** tab.

**Step 2:** Click **Create user**.

**Step 3:** Configure the first account (Admin):
- Select **Send an email invitation**.
- Enter the **Email address** (use your real email to receive the password, or any address you control).
- Under **Password**: select **Generate a password** or set one yourself.
- Click **Create user**.
![Fill in the form to create the Admin-test user](/images/5-Workshop/5.9-api-gateway-and-auth/3.4-create-user-admin-test.png?featherlight=false&width=90pc)

**Step 4:** Repeat Steps 2 and 3 to create 2 more accounts (e.g., QA and Developer). Once done, you'll see all 3 accounts with status **Force change password** (requiring a password change on first login).
![3 test accounts created](/images/5-Workshop/5.9-api-gateway-and-auth/3-test-users-created.png?featherlight=false&width=90pc)

---

#### Part 3: Create the API Gateway and Configure the Cognito Authorizer

**Step 1:** In the AWS Console search bar, search for `API Gateway`, select **API Gateway**.

**Step 2:** Find the **HTTP API** card (the most common choice for modern Serverless/Lambda applications) and click **Build**.
![Select HTTP API to Build](/images/5-Workshop/5.9-api-gateway-and-auth/5.3-api-gateway-http-build.png?featherlight=false&width=90pc)

**Step 3:** Name the API `playwright-api`. Click **Next** through to the Review screen, then click **Create**.
![Create the playwright-api API](/images/5-Workshop/5.9-api-gateway-and-auth/5.5-api-integration-lambda.png?featherlight=false&width=90pc)

![API playwright-api created successfully](/images/5-Workshop/5.9-api-gateway-and-auth/7b-api-created.png?featherlight=false&width=90pc)

**Step 4:** Configure CORS (so the Frontend on a different domain can call the API):
- In the left-hand menu, select **CORS**.
- Configure **Access-Control-Allow-Origin**: enter `*` (or your CloudFront domain later) then click **Add**.
- Configure **Access-Control-Allow-Headers**: enter `*` or the required headers (such as `Authorization`, `Content-Type`).
- Configure **Access-Control-Allow-Methods**: select `*` (or GET, POST, PUT, DELETE, OPTIONS).
- Click **Save**.

![Configure CORS in API Gateway](/images/5-Workshop/5.9-api-gateway-and-auth/3.4.CORS.png?featherlight=false&width=90pc)

**Step 5:** Add Routes and Integrations (connect to Lambda):
- In the left-hand menu, select **Routes** → click **Create**.
- Add the routes matching the Frontend:
  - **Test**: `POST /trigger` · `GET /test-runs` · `GET /test-runs/{id}` · `GET /stats` · `GET /test-suites`
  - **Schedule**: `GET /schedules` · `POST /schedules` · `DELETE /schedules/{id}`
  - **Config & Users**: `GET /email-config` · `POST /email-config` · `GET /users` · `GET /audit-logs`
  - **Report**: `GET /reports` · `GET /AI-insights`
- Switch to the **Integrations** menu, select each route you just created → click **Attach integration** → select **AWS Lambda** → select the corresponding Lambda function created in section 5.7 → click **Create**.

![List of routes created in API Gateway](/images/5-Workshop/5.9-api-gateway-and-auth/3.5.Routes.png?featherlight=false&width=90pc)

**Step 6:** Configure the Cognito Authorizer (secure the API):

**Go to Authorization:**
- In the left-hand menu under **Develop**, select **Authorization**.

**Create the Authorizer:**
- Click the **Manage authorizers** tab (next to the Attach authorizers to routes tab) → click **Create**.
- Fill in the configuration:
  - **Authorizer type**: Select `JWT`.
  - **Name**: Enter `cognito-authorizer`.
  - **Identity source**: Enter `$request.header.Authorization` (AWS pre-fills this).
  - **Issuer URL**: Enter `https://cognito-idp.<region>.amazonaws.com/<User_Pool_ID>`
  - **Audience**: Click **Add audience** then enter the App Client ID from Part 1.
![Authorization section in the left-hand menu](/images/5-Workshop/5.9-api-gateway-and-auth/2.6%20taoAPIbaomat.png?featherlight=false&width=90pc)

- Click **Create** to finish.

![Authorizer cognito-authorizer created](/images/5-Workshop/5.9-api-gateway-and-auth/8a-authorizer-created.png?featherlight=false&width=90pc)

**Attach the Authorizer to the Routes:**
- Switch back to the **Attach authorizers to routes** tab.
- Select the routes that need to be secured (such as `/trigger`, `/test-runs`, etc.).
- In the **Select authorizer** field, select the `cognito-authorizer` you just created → click **Attach authorizer**.

![Route with the JWT Authorizer attached](/images/5-Workshop/5.9-api-gateway-and-auth/8b-route-jwt-attached.png?featherlight=false&width=90pc)

**Step 7:** Get the Invoke URL:
- Go back to the HTTP API overview page (**Stages** section → select stage `$default` or `prod`).
- You'll see an **Invoke URL** in the form: `https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com`.
- Copy this URL. This is the `API_BASE_URL` you'll paste into `config.js` in Part 1 - Step 2 of section 5.10.
![Stage $default with the Invoke URL](/images/5-Workshop/5.9-api-gateway-and-auth/9-stage-invoke-url.png?featherlight=false&width=90pc)

---

#### Summary of Results

At this point, you have the 3 key values needed for section 5.10:
- **API_BASE_URL** (Invoke URL)
- **userPoolId** (Cognito User Pool ID)
- **clientId** (Cognito App Client ID)

---

Next, we will move on to **[5.10. Frontend](../5.10-frontend/)** to deploy the Dashboard UI to S3 and CloudFront.
