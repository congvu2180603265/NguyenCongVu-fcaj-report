---
title : "Amazon EventBridge"
date : 2026-07-10
weight : 5
chapter : false
pre : " <b> 5.13.5. </b> "
---

#### Delete Amazon EventBridge

1. Go to **Amazon EventBridge Console** → **Buses** → **Rules**. Select the event bus **default**. Under the **Event pattern rules** tab, select the rule `playwright-postprocessing-trigger` (Status: **Enabled**), then click the **Delete** button.
![EventBridge 1](/images/5-Workshop/5.13-cleanup/5.13.5-eventbridge/eventbridge1.png?featherlight=false&width=90pc)

2. A **Delete rule?** dialog appears, confirming the deletion of rule `playwright-postprocessing-trigger` and warning that it will impact related resources. Type `delete` in the confirmation field and click **Delete**.
![EventBridge 2](/images/5-Workshop/5.13-cleanup/5.13.5-eventbridge/eventbridge2.png?featherlight=false&width=90pc)

3. Upon success, the **Event pattern rules** list returns to **No rules** — showing **No rules to display**.
![EventBridge 3](/images/5-Workshop/5.13-cleanup/5.13.5-eventbridge/eventbridge3.png?featherlight=false&width=90pc)
