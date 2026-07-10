---
title : "Create S3 Buckets"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.2.4. </b> "
---

#### Create Amazon S3 buckets

In this section, you will create two S3 buckets in **Asia Pacific (Singapore) (`ap-southeast-1`)**:

| Bucket | Purpose |
|---|---|
| `playwright-webui-12` | Stores the Dashboard Console frontend files |
| `playwright-report-2026` | Stores reports, screenshots, and test-result data |

{{% notice note %}}
S3 bucket names must be unique. If a name used in this guide is unavailable, add your own suffix and use the same name in all subsequent configuration steps.
{{% /notice %}}

---

### Step 1: Create the Web UI bucket

Sign in to the **AWS Management Console**, open **Amazon S3**, and choose **Create bucket**.

Configure:

| Property | Value |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | `General purpose` |
| Bucket namespace | `Global namespace` |
| Bucket name | `playwright-webui-12` |
| Object Ownership | `ACLs disabled` |

Keep the remaining settings at their defaults and choose **Create bucket**.

![Create the Web UI bucket](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/create-webui-bucket.png?featherlight=false&width=90pc)

---

### Step 2: Upload the frontend build

Open `playwright-webui-12`, choose **Upload**, and upload all contents inside the frontend `dist` directory to the bucket root. Do not upload `dist` itself as a parent directory.

After the upload, the bucket should contain `index.html` and the frontend assets, for example:

```text
assets/
favicon.svg
icons.svg
index.html
```

![Objects in the Web UI bucket](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/webui-bucket-objects.png?featherlight=false&width=90pc)

---

### Step 3: Configure static website hosting

In the Web UI bucket, open:

```text
Properties
→ Static website hosting
→ Edit
```

Select:

| Property | Value |
|---|---|
| Static website hosting | `Enable` |
| Hosting type | `Host a static website` |
| Index document | `index.html` |

For a single-page application, you can also set the **Error document** to `index.html`. Choose **Save changes**.

![Enable static website hosting](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/edit-static.png?featherlight=false&width=90pc)

{{% notice warning %}}
Do not make the report bucket public. For production, keep the Web UI bucket private and let CloudFront access it through Origin Access Control (OAC). When using OAC, use the S3 bucket origin rather than the S3 website endpoint.
{{% /notice %}}

---

### Step 4: Create the report bucket

Return to the S3 bucket list, choose **Create bucket**, and configure:

| Property | Value |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | `General purpose` |
| Bucket namespace | `Global namespace` |
| Bucket name | `playwright-report-2026` |
| Block Public Access | Keep all options enabled |

Keep the remaining settings at their defaults and choose **Create bucket**.

![Create the report bucket](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/create-report-bucket.png?featherlight=false&width=90pc)

---

### Step 5: Create the report storage structure

Open `playwright-report-2026` and create these folders:

```text
json/
reports/
screenshots/
test-scripts/
```

These folders store JSON results, HTML reports, test screenshots, and test scripts, respectively.

![Report bucket structure](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/report-bucket-objects.png?featherlight=false&width=90pc)

---

### Step 6: Verify the result

Return to **Amazon S3 → Buckets** and confirm that both buckets exist in `ap-southeast-1`:

```text
playwright-webui-12
playwright-report-2026
```

![Verify the created buckets](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/verify-buckets.png?featherlight=false&width=90pc)

The Web UI bucket is now ready for the CloudFront configuration step, and the report bucket is ready for Lambda to store test results.
