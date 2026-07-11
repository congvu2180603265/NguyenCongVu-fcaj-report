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

**Step 3:** Keep **all 4 options checked** (Block all public access = ON) — since only CloudFront will access it, via Origin Access Control, not the bucket directly.

![Block Public Access Configuration](/images/5-Workshop/5.10-frontend/block-public-access-config.png?featherlight=false&width=90pc)

**Step 4:** Click **Save changes**, type `confirm` in the confirmation box, click **Confirm**.

---

#### Part 4: Create a CloudFront Distribution

**Step 1:** In the AWS Console search bar, search for `CloudFront`, select **CloudFront**.
![alt text](/images/5-Workshop/5.10-frontend/4.1.png?featherlight=false&width=90pc)

**Step 2:** Click the orange **Create distribution** button.

**Step 3:** Origin domain: click the field, select bucket `playwright-webui-xxx.s3.ap-southeast-1.amazonaws.com` from the suggestion list.  
![alt text](/images/5-Workshop/5.10-frontend/4.3.png?featherlight=false&width=90pc)
**Step 4:** Origin access: select **Origin access control settings (recommended)** → click **Create control setting** → keep the default Signing behavior = **Sign requests (recommended)** → **Create**.
**Step 5:** Scroll down to **Default root object**, enter `index.html`.
![alt text](/images/5-Workshop/5.10-frontend/4.5.png?featherlight=false&width=90pc)
**Step 6:** Leave the remaining settings (Price class, WAF, etc.) at default; you may set a Name of your choosing (e.g. `playwright-cloudfront-123`). Click **Create distribution**.
![alt text](/images/5-Workshop/5.10-frontend/4.2.png?featherlight=false&width=90pc)
**Step 7:** Confirm the message **"Successfully created new distribution"** — note down the **Distribution domain name** (e.g. `d2qnbza21ik9r.cloudfront.net`); **Last modified** will now show status **Deploying**.
![alt text](/images/5-Workshop/5.10-frontend/4.7.png?featherlight=false&width=90pc)
**Step 8:** After creation, CloudFront shows a yellow banner: **"The S3 bucket policy needs to be updated"** → click **Copy policy**.
![alt text](/images/5-Workshop/5.10-frontend/4.8.png?featherlight=false&width=90pc)
**Step 9:** Open a new tab, go back to the **S3 bucket** `playwright-webui-xxx` → **Permissions** tab → scroll down to **Bucket policy** → click **Edit** → paste the copied policy → **Save changes**.
![alt text](/images/5-Workshop/5.10-frontend/4.9.png?featherlight=false&width=90pc)
**Step 10:** Go back to the **Cognito Console** → User pool `playwright-user-pool` → **App integration** → App client `playwright-app` → **Edit** → update **Callback URL(s)** to the CloudFront Domain Name (replacing the temporary `http://localhost:3000` from section 5.9) → **Save changes**.
![alt text](/images/5-Workshop/5.10-frontend/4.10.png?featherlight=false&width=90pc)

---

#### Part 5: Get the Domain Name and Verify

**Step 1:** Go back to the **CloudFront Console** → select the Distribution you just created.

**Step 2:** Wait for the **Status** column to switch from **Deploying** to **Enabled** (usually takes 3-5 minutes).

**Step 3:** Copy the **Domain Name** shown in the Console, in the form `dxxxxxxxxxxxxx.cloudfront.net`.

**Step 4:** Open the Domain Name in a browser (use an incognito tab to avoid caching), confirm the Dashboard shows the correct login screen.
![alt text](/images/5-Workshop/5.10-frontend/5.4.png?featherlight=false&width=90pc)
**Step 5:** Try accessing the S3 URL directly, in the form `https://playwright-webui-xxx.s3.ap-southeast-1.amazonaws.com/index.html` — it should be blocked, returning a **403 Forbidden** error.

![alt text](/images/5-Workshop/5.10-frontend/5.5.png?featherlight=false&width=90pc)

---

#### Verification

- Open the CloudFront Domain Name in a browser, the Dashboard displays correctly.
- Direct access to the S3 URL is blocked (403); it must go through CloudFront to be viewable.
- Log in with one of the 3 test accounts (Admin/QA/Developer) created in section 5.9, confirm login succeeds and the API can be called (no CORS/401 errors).

**Note:** CloudFront caches content — if you rebuild and re-upload the Frontend but don't see the changes, go to CloudFront Console → select the Distribution → **Invalidations** tab → **Create invalidation** → enter `/*` for Object paths → **Create invalidation**.

---

Next, we will move on to **[5.11. EventBridge](../5.11-eventbridge/)** to configure automated post-processing triggers.
