---
title : "EventBridge"
date : 2026-07-10
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Configure Amazon EventBridge to Trigger Post-Processing

In this section, you will configure an Amazon EventBridge rule to automatically trigger the `playwright-postprocessing` Lambda function when an ECS task completes (either successfully or with a failure), enabling automated report processing and AI-powered log analysis.

{{% notice note %}}
Content for this section will be updated. Ensure the `playwright-postprocessing` Lambda function is deployed (section 5.7) and the ECS Cluster is configured (section 5.6) before proceeding.
{{% /notice %}}

---

Next, we will move on to **[5.12. End-to-End Test](../5.12-end-to-end-test/)** to verify the full system flow.
