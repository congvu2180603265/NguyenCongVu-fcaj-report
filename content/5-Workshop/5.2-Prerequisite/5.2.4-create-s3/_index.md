---
title : "Create Amazon S3 Buckets"
date : 2026-06-13
weight : 4
chapter : false
pre : " <b> 5.2.4. </b> "
---

#### Create Amazon S3 Buckets

In this section, we will create two S3 buckets in the **Asia Pacific (Singapore) (`ap-southeast-1`)** Region:

| Bucket | Purpose |
|---|---|
| `playwright-webui-12` | Stores the Dashboard Console frontend files |
| `playwright-report-2026` | Stores reports, screenshots, and test result data |

{{% notice note %}}
S3 bucket names must be globally unique. If the names in this guide are already taken, add your own suffix and use that same name in later configuration steps.
{{% /notice %}}

---

**Step 1:** Create the Web UI bucket

Sign in to the **AWS Management Console**, open **Amazon S3**, then select **Create bucket**.

Configure:

| Property | Value |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | `General purpose` |
| Bucket namespace | `Global namespace` |
| Bucket name | `playwright-webui-12` |
| Object Ownership | `ACLs disabled` |

Leave the remaining settings as default and select **Create bucket**.

![Create the Web UI bucket](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/create-webui-bucket.png?featherlight=false&width=90pc)

---

**Step 2:** Upload the frontend source to S3

Open bucket `playwright-webui-12`, select **Upload**, then upload the entire contents inside the `dist` build folder to the bucket's root directory. Do not upload the `dist` folder itself as a parent folder.

After uploading, the bucket should contain `index.html` and the frontend assets, for example:

```text
assets/
favicon.svg
icons.svg
index.html
```

![Files in the Web UI bucket](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/webui-bucket-objects.png?featherlight=false&width=90pc)

---

**Step 3:** Configure static website hosting

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

If the frontend is a SPA, you can set **Error document** to `index.html` as well. Then select **Save changes**.

![Enable static website hosting](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/edit-static.png?featherlight=false&width=90pc)

{{% notice warning %}}
Do not make the report bucket public. For a production environment, keep the Web UI bucket private and let CloudFront access it via Origin Access Control (OAC). When using OAC, use the S3 bucket origin instead of the S3 website endpoint.
{{% /notice %}}

---

**Step 4:** Create the report bucket

Go back to the S3 bucket list, select **Create bucket** and configure:

| Property | Value |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | `General purpose` |
| Bucket namespace | `Global namespace` |
| Bucket name | `playwright-report-2026` |
| Block Public Access | Keep all options enabled |

Leave the remaining settings as default and select **Create bucket**.

![Create the report bucket](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/create-report-bucket.png?featherlight=false&width=90pc)

---

**Step 5:** Create the report storage structure

Open bucket `playwright-report-2026` and create the following folders:

```text
json/
reports/
screenshots/
test-scripts/
```

These folders store JSON results, HTML reports, test screenshots, and test scripts, respectively.

![Report bucket structure](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/report-bucket-objects.png?featherlight=false&width=90pc)

---

**Step 6:** Verify the result

Go back to **Amazon S3 → Buckets** and confirm both buckets were created in `ap-southeast-1`:

```text
playwright-webui-12
playwright-report-2026
```

![Confirm the buckets were created](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/verify-buckets.png?featherlight=false&width=90pc)

Once done, the Web UI bucket is ready for the CloudFront configuration step, and the report bucket is ready for Lambda to store test results.
