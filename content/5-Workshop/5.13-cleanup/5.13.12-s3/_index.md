---
title : "Amazon S3"
date : 2026-07-10
weight : 12
chapter : false
pre : " <b> 5.13.12. </b> "
---

#### Delete Amazon S3

There are 2 S3 buckets to delete: `playwright-webui-12` (static website hosting) and `playwright-report-2026` (test reports). Each bucket must be emptied before deletion.

1. Go to **Amazon S3 Console** → **Buckets** → **General purpose buckets**. The list shows **General purpose buckets (1/2)** with `playwright-webui-12` selected. Click **Empty** first to empty the bucket content, then click **Delete**.
![S3 1](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s31.png?featherlight=false&width=90pc)

2. On the **Delete bucket** page for `playwright-webui-12`, review the warnings (bucket names are unique, this bucket is configured to host a static website). Enter `playwright-webui-12` in the confirmation field and click **Delete bucket**.
![S3 2](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s32.png?featherlight=false&width=90pc)

3. The system displays a green banner **"Successfully deleted bucket 'playwright-webui-12'"** and the list returns to **General purpose buckets (1)** showing only `playwright-report-2026` remaining.
![S3 3](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s33.png?featherlight=false&width=90pc)

4. Repeat the empty and delete steps for `playwright-report-2026`. Upon success, the system displays **"Successfully deleted bucket 'playwright-report-2026'"** and the **General purpose buckets (0)** list shows **No buckets** — **You don't have any buckets**.
![S3 4](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s34.png?featherlight=false&width=90pc)
