---
title : "EventBridge"
date : 2026-07-10
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Configure EventBridge Scheduler for Automated Test Runs

In this section, you will create a recurring schedule with Amazon EventBridge Scheduler. At the configured time, Scheduler sends a JSON message to the `playwright-task-queue` SQS queue; the downstream system consumes the message and starts the Playwright test.

{{% notice note %}}
Ensure that the `playwright-task-queue` SQS queue was created in section 5.4. The example below runs **every day at 08:00** in the **Asia/Saigon** time zone. Adjust the schedule and message for your environment.
{{% /notice %}}

---

#### Part 1: Create the Schedule

**Step 1:** Open the **Amazon EventBridge Console**. From the left menu, choose **Scheduler → Schedules**, then choose **Create schedule**.

![Open EventBridge Schedules and create a schedule](/images/5-Workshop/5.11-eventbridge/1-eventbridge-home.png?featherlight=false&width=90pc)

**Step 2:** For **Schedule name**, enter `playwright-daily-trigger`. Keep the **Schedule group** as `default`.

![Enter the schedule name](/images/5-Workshop/5.11-eventbridge/2-schedule-name.png?featherlight=false&width=90pc)

**Step 3:** Under **Schedule pattern**, configure:

- **Occurrence:** Recurring schedule
- **Time zone:** `(UTC+07:00) Asia/Saigon`
- **Schedule type:** Cron-based schedule
- **Cron expression:** `cron(0 8 * * ? *)`

This expression runs at **08:00 every day** in the selected time zone. Verify the **Next 10 trigger dates**, then choose **Next**.

![Configure the daily 08:00 cron schedule](/images/5-Workshop/5.11-eventbridge/3-schedule-cron.png?featherlight=false&width=90pc)

---

#### Part 2: Select SQS as the Target

**Step 1:** On **Select target**, keep **Templated targets**, then select **Amazon SQS – SendMessage**.

![Select Amazon SQS as the target](/images/5-Workshop/5.11-eventbridge/4-select-target.png?featherlight=false&width=90pc)

**Step 2:** Select `playwright-task-queue` and enter the following **MessageBody**:

```json
{
  "target_url": "https://google.com",
  "test_script": "test-script.js",
  "triggered_by": "schedule"
}
```

Replace `target_url` and `test_script` with your actual test URL and script, then choose **Next**.

![Configure the queue and message body](/images/5-Workshop/5.11-eventbridge/5-schedule-message.png?featherlight=false&width=90pc)

---

#### Part 3: Configure Permissions and Create the Schedule

**Step 1:** Under **Settings**, turn on **Enable schedule**. For a recurring schedule, keep **Action after schedule completion** as `NONE`. The default Retry and DLQ settings are sufficient for this workshop.

![Enable the schedule and review its settings](/images/5-Workshop/5.11-eventbridge/6-schedule-settings.png?featherlight=false&width=90pc)

**Step 2:** Under **Permissions**, select **Create new role for this schedule**. EventBridge Scheduler creates an execution role that can send messages to the selected SQS queue. Keep the default AWS-owned encryption key, then choose **Next**.

![Create an execution role for Scheduler](/images/5-Workshop/5.11-eventbridge/7-schedule-role.png?featherlight=false&width=90pc)

**Step 3:** On **Review and create schedule**, verify the name, time zone, cron expression, SQS target, and execution role. Choose **Create schedule**.

![Review the schedule configuration](/images/5-Workshop/5.11-eventbridge/8-schedule-review.png?featherlight=false&width=90pc)

**Step 4:** Confirm that the new schedule has the **Enabled** status. Review its cron expression, target, and retry policy on the details page.

![The EventBridge schedule is created and enabled](/images/5-Workshop/5.11-eventbridge/9-schedule-created.png?featherlight=false&width=90pc)

---

#### Part 4: Create an Event Pattern Rule for the Post-processing Lambda

Besides the recurring schedule from Parts 1-3 (under **Scheduler**), the system also needs **a separate Rule** under **Buses → Rules** to automatically trigger the `playwright-postprocessing` Lambda as soon as the Fargate Task finishes — regardless of whether the task was triggered manually, on schedule, or any other way.

{{% notice note %}}
"Rules" and "Schedules" are 2 different concepts in EventBridge. **Schedules** (Parts 1-3) run at a predetermined time. **Rules** (Part 4) fire on an **event pattern** — here, the ECS Task switching to STOPPED status, regardless of how that task was triggered.
{{% /notice %}}

**Step 1:** Open the **Amazon EventBridge Console**, in the left menu choose **Buses → Rules**, on the **Event pattern rules** tab click the orange **Create rule** button.

![Open the Rules page and click Create rule](/images/5-Workshop/5.11-eventbridge/10-open-rules-create.png?featherlight=false&width=90pc)

**Step 2:** On the **Create rule** page, under **Builder mode** at the top, keep **Enhanced builder** (Visual drag-and-drop builder) — this is the default mode.

**Step 3:** Switch to the **Configure** tab (next to the **Build** tab in the middle of the screen), set **Name** = `playwright-postprocessing-trigger`. Keep the event bus on the **Build** tab as the default `default`.

![Name the rule on the Configure tab](/images/5-Workshop/5.11-eventbridge/11-configure-name.png?featherlight=false&width=90pc)

**Step 4:** Go back to the **Build** tab. Scroll down to **Triggering events** below the canvas; the **Event pattern (filter)** panel on the right is ready for input (empty, line 1, may briefly show **"JSON is invalid: Unexpected end of JSON input"** — normal since nothing has been entered yet). Click into this panel and paste the following JSON pattern:

```json
{
  "source": ["aws.ecs"],
  "detail-type": ["ECS Task State Change"],
  "detail": {
    "lastStatus": ["STOPPED"],
    "clusterArn": ["arn:aws:ecs:ap-southeast-1:<account-id>:cluster/playwright-cluster"]
  }
}
```

Replace `<account-id>` with your real Account ID.

![Paste the event pattern JSON into the Event pattern (filter) panel](/images/5-Workshop/5.11-eventbridge/12-paste-json-pattern.png?featherlight=false&width=90pc)

**Step 5:** In the left column, click the **Targets** tab (next to **Events**), in the target search box select **AWS service** → **Lambda function** → select the `playwright-postprocessing` function. Drag it into the **Targets** panel on the right of the canvas (or click to select directly, depending on the UI).

![Select the playwright-postprocessing Lambda as the Target](/images/5-Workshop/5.11-eventbridge/13-select-lambda-target.png?featherlight=false&width=90pc)

**Step 6:** Once both the **Triggering Events** and **Targets** panels no longer show red warnings, click the orange **Create** button in the top-right corner.

**Step 7:** Confirm the `playwright-postprocessing-trigger` rule appears in the **Event pattern rules** list, with status **Enabled**, Type = **Standard**, Event bus = **default**.

![Rule playwright-postprocessing-trigger created, status Enabled](/images/5-Workshop/5.11-eventbridge/14-rule-created.png?featherlight=false&width=90pc)

{{% notice warning %}}
The AWS Console shows a banner: **"We have moved Scheduled rules in your account to 'Scheduler' section"** — this is just a notice about the legacy Schedule feature moving to the new Scheduler section, and does not affect the Event pattern Rule you just created here.
{{% /notice %}}

---

#### Verification

- Confirm that the schedule is **Enabled** and its next invocation matches the `Asia/Saigon` time zone.
- At the scheduled time, monitor `playwright-task-queue` in SQS and confirm that a message is delivered.
- Confirm the `playwright-postprocessing-trigger` rule is **Enabled**.
- After a Fargate Task finishes and switches to STOPPED, verify the `playwright-postprocessing` Lambda is automatically triggered (check that Lambda's CloudWatch Logs to confirm).
- Check the CloudWatch Logs for the SQS/ECS processing component and confirm that the test starts with the expected `target_url` and `test_script`.
- To test immediately, temporarily set the schedule to a near-future time, verify the result, and then restore the daily schedule.

{{% notice warning %}}
EventBridge Scheduler cron expressions contain six fields. You cannot specify concrete values for both **day-of-month** and **day-of-week**; use `?` for one of these fields.
{{% /notice %}}

---

Next, proceed to **[5.12. End-to-End Test](../5.12-end-to-end-test/)** to verify the complete system flow.
