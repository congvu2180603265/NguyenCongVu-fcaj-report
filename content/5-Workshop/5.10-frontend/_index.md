---
title : "Frontend"
date : 2026-06-21
weight : 10
chapter : false
pre : " <b> 5.10. </b> "
---

> **Note**: If you're not using CloudFront + S3 for the Web UI, you can **skip this entire section**. If the frontend is hosted elsewhere (Vercel, Amplify, or run locally for a demo), just make sure you've updated the correct Invoke URL (section 5.9) and that the Cognito App Client ID/Callback URL matches the actual domain being used.

#### Introduction

Deploy the Dashboard UI to S3 and distribute it through CloudFront.

---

#### Part 1: Update Configuration and Build the Frontend

**Step 1:** Open the React source code (Dashboard, login, test history, report viewing) in VS Code (or an equivalent IDE).

**Step 2:** Open the config file (e.g. `src/config.js`), update it:

```js
// ===== AWS CONFIGURATION =====
// Update these values after deploying to AWS

export const API_BASE_URL = 'https://8bsb7jbhu7.execute-api.ap-southeast-1.amazonaws.com'

export const COGNITO_CONFIG = {
  region: 'ap-southeast-1',
  userPoolId: 'ap-southeast-1_DXMd3Q6ee',
  clientId: '492jkd32vd241c2tuv1v160g4o',
}
```

Here, `API_BASE_URL` is the **Invoke URL** from section 5.9, and `userPoolId`/`clientId` come from Cognito Console → User pool → App integration.

**Step 3:** Verify the API endpoints below are correctly built from `API_BASE_URL`:

```js
// API endpoints
export const API_ENDPOINTS = {

  trigger:    `${API_BASE_URL}/trigger`,
  testRuns:   `${API_BASE_URL}/test-runs`, 
  stats:      `${API_BASE_URL}/stats`,
  testSuites: `${API_BASE_URL}/test-suites`,
  schedules:  `${API_BASE_URL}/schedules`,
  emailConfig:`${API_BASE_URL}/email-config`,
  
  users:      `${API_BASE_URL}/users`,
  auditLogs:  `${API_BASE_URL}/audit-logs`,

  reports:         `${API_BASE_URL}/reports`,
  chatgptInsights: `${API_BASE_URL}/AI-insights`,
}
```

![config.js updated with the Invoke URL and Cognito settings](/images/5-Workshop/5.10-frontend/1-config-js-updated.png?featherlight=false&width=90pc)

**Step 4:** Open a Terminal in VS Code (or a separate terminal), `cd` into the Frontend project directory, run:

```bash
npm run build
```

**Step 5:** Wait until you see the line `✓ built in ...ms`, confirm the `dist` folder was created with the files (`index.html`, `assets/index-xxxx.css`, `assets/index-xxxx.js`, etc.), ready to upload to S3.

![Build succeeded, dist folder created](/images/5-Workshop/5.10-frontend/2-npm-build-success.png?featherlight=false&width=90pc)

---

#### Part 2: Upload to S3

**Step 1:** Go to the **S3 Console** → open bucket `playwright-webui-xxx`.

**Step 2:** Click the **Upload** button.

**Step 3:** Click **Add files** to add `favicon.svg`, `icons.svg`, `index.html`; click **Add folder** to add the `assets/` folder (containing the built `.css` and `.js` files). Confirm the **Files and folders** panel shows **5 total** files (`index-xxxx.css`, `index-xxxx.js`, `favicon.svg`, `icons.svg`, `index.html`).

![List of files ready to upload to S3](/images/5-Workshop/5.10-frontend/3-s3-upload-file-list.png?featherlight=false&width=90pc)

**Step 4:** Scroll to the bottom of the page, click **Upload**, wait until the **"Upload succeeded"** message appears.

---

#### Part 3: Configure Block Public Access

**Step 1:** Go to bucket `playwright-webui-xxx` → **Permissions** tab.

**Step 2:** Scroll down to **Block public access (bucket settings)**, click **Edit**.

![Block public access section in Permissions tab](/images/5-Workshop/5.10-frontend/3.2-s3-block-public-access.png?featherlight=false&width=90pc)

**Step 3:** Keep **all 4 options checked** (Block all public access = ON) — since only CloudFront will access it, via Origin Access Control, not the bucket directly.

![Tick Block all public access](/images/5-Workshop/5.10-frontend/3.3-s3-edit-block-public-access.png?featherlight=false&width=90pc)

**Step 4:** Click **Save changes**, type `confirm` in the confirmation box, click **Confirm**.

---

#### Part 4: Create a CloudFront Distribution

**Step 1:** In the AWS Console search bar, search for `CloudFront` → Select **CloudFront** → Click the orange **Create distribution** button. In **Step 1: Choose a plan**, ensure the **Free ($0/month)** plan is selected (it should be selected by default) → Scroll down to the bottom right corner and click the **Next** button.

![Choose Free plan](/images/5-Workshop/5.10-frontend/4.2-cloudfront-choose-plan.png?featherlight=false&width=90pc)

**Step 2 (Get started):** In the **Distribution options** section, enter a name in the **Distribution name** field (e.g., `playwright-cloudfront-123`), then scroll to the bottom of the page and click **Next**.

![CloudFront Console](/images/5-Workshop/5.10-frontend/4.1-cloudfront-console.png?featherlight=false&width=90pc)

**Step 3 (Specify origin):** This is where you configure the S3 Bucket:
- **Origin domain**: Click the field and select bucket `playwright-webui-xxx.s3.ap-southeast-1.amazonaws.com`.
- **Origin access**: Select **Origin access control settings (recommended)** → Click **Create new OAC** → Keep the default settings and click **Create**.

![Specify origin and Origin access settings](/images/5-Workshop/5.10-frontend/4.3-cloudfront-specify-origin.png?featherlight=false&width=90pc)

**Step 4 (Settings):** Continue scrolling down to the **Settings** section, check **Allow private S3 bucket access to CloudFront**, keep the other recommended settings below, and click **Next**.

![Set Origin Settings](/images/5-Workshop/5.10-frontend/4.4-cloudfront-origin-settings.png?featherlight=false&width=90pc)

**Step 5:** Click **Next** to proceed through the remaining steps → When you reach the final step (Review and create), click **Create distribution**.

![Review and create distribution](/images/5-Workshop/5.10-frontend/4.5-cloudfront-review-create.png?featherlight=false&width=90pc)

**Step 6 (Configure Default Root Object):** After creation, on the Distribution overview page, scroll down to the **Settings** section → Click **Edit**.

![Distribution overview page, scroll down to Settings](/images/5-Workshop/5.10-frontend/4.6.1.png?featherlight=false&width=90pc)

In the **Default root object** field, enter `index.html`. Scroll to the bottom of the page → Click **Save changes**.

![Enter index.html in Default root object and Save changes](/images/5-Workshop/5.10-frontend/4.6.2.png?featherlight=false&width=90pc)

**Step 7 (Update S3 Bucket Policy):** CloudFront will show a yellow banner **"The S3 bucket policy needs to be updated"** → Click **Copy policy** → Open a new tab, go to the **S3 bucket** (`playwright-webui-xxx`) → **Permissions** tab → Scroll down to **Bucket policy** and click **Edit** → Paste the copied policy → Click **Save changes**.

> 💡 **Note:** This grants CloudFront permission to read content from S3, while preventing external users from directly accessing S3 via public links (AWS security best practice).

![Paste policy into S3 Bucket Policy](/images/5-Workshop/5.10-frontend/4.9-s3-bucket-policy-updated.png?featherlight=false&width=90pc)

**Step 8 (Update Callback URL):** Go back to the **Cognito Console** → User pool → **App integration** → App client → Click **Edit** → Update **Callback URL(s)** to the CloudFront Domain Name (e.g. `https://dxxxx.cloudfront.net`) → Click **Save changes**.

> 💡 **Note:** You need to replace the temporary `http://localhost:3000` with the CloudFront Domain so that after successful login, Cognito knows to redirect users back to the actual web interface.

![Update Callback URL in Cognito App Client](/images/5-Workshop/5.10-frontend/4.10-cognito-callback-url-updated.png?featherlight=false&width=90pc)

---

#### Part 5: Verification and Finalization

**Step 1:** Go back to the **CloudFront Console**, wait for the **Status** column to switch from **Deploying** to **Enabled** (usually takes 3-5 minutes).

![Distribution deploying, wait for Enabled status](/images/5-Workshop/5.10-frontend/4.7-cloudfront-deploying.png?featherlight=false&width=90pc)

**Step 2:** Copy the **Domain Name** (e.g. `dxxxxxxxxxxxxx.cloudfront.net`) and open it in a browser (preferably in an incognito tab) to verify the interface displays correctly.

![Dashboard showing the correct login UI](/images/5-Workshop/5.10-frontend/5.4-cloudfront-dashboard-login.png?featherlight=false&width=90pc)

- Direct access to the S3 URL should be blocked (returning a **403 Forbidden** error); it must go through CloudFront to be viewable.

![Direct S3 access blocked 403 Forbidden](/images/5-Workshop/5.10-frontend/5.5-s3-direct-403-forbidden.png?featherlight=false&width=90pc)

- Try logging in with one of the 3 test accounts (Admin/QA/Developer) created in section 5.9, confirm the login is successful and APIs can be called (no CORS/401 errors).

> 💡 **Note:** If you modify the Frontend code and re-upload it to S3 later but don't see the changes on the web, go to the **Invalidations** tab in CloudFront → **Create invalidation** with the object path `/*` to clear the old cache.

---

Next, we will move on to **[5.11. EventBridge](../5.11-eventbridge/)** to configure automated post-processing triggers.
