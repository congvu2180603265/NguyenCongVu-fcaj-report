---
title : "CloudFront"
date : 2026-07-10
weight : 1
chapter : false
pre : " <b> 5.13.1. </b> "
---

#### Delete CloudFront

You cannot directly delete an active CloudFront Distribution. You must first cancel the Free plan and disable the distribution before deleting it.

1. Go to **CloudFront Console** → **Distributions**. Select the distribution `E3APVVDEOB1F3Z` (`playwright-cloudfront-123`), then click the **Disable** button in the toolbar. A **Disable distribution?** confirmation dialog appears → click **Disable** to proceed.
![CloudFront 1](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/1.png?featherlight=false&width=90pc)

2. Click the distribution ID to go to the detail page. Under the **Billing** section, the current plan is **Free plan ($0/month)** → click **Manage plan** to cancel this plan.
![CloudFront 2](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/2.png?featherlight=false&width=90pc)

3. A **Review change: Free to Pay-as-you-go** dialog appears, confirming the switch from Free plan to Pay-as-you-go. Click **Cancel plan** to cancel the Free plan.
![CloudFront 3](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/3.png?featherlight=false&width=90pc)

4. After canceling successfully, the system returns to the distribution detail page with **Billing** now showing **Pay-as-you-go** and the status showing **Deploying** while the update propagates.
![CloudFront 4](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/4.png?featherlight=false&width=90pc)

5. Return to the **Distributions** list. Select the distribution with Status **Disabled**, then click **Delete**. A **Delete distribution?** confirmation dialog appears → click **Delete** to confirm.
![CloudFront 5](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/5.png?featherlight=false&width=90pc)

6. Upon success, the system displays a green banner **"Deleted distribution: E3APVVDEOB1F3Z."** and the list returns to **No distributions**.
![CloudFront 6](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/6.png?featherlight=false&width=90pc)
