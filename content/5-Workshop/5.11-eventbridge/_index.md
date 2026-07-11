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

![Open EventBridge Schedules and create a schedule](/images/5-Workshop/5.11-eventbridge/eventbridge-home.png?featherlight=false&width=90pc)

**Step 2:** For **Schedule name**, enter `playwright-daily-trigger`. Keep the **Schedule group** as `default`.

![Enter the schedule name](/images/5-Workshop/5.11-eventbridge/schedule-name.png?featherlight=false&width=90pc)

**Step 3:** Under **Schedule pattern**, configure:

- **Occurrence:** Recurring schedule
- **Time zone:** `(UTC+07:00) Asia/Saigon`
- **Schedule type:** Cron-based schedule
- **Cron expression:** `cron(0 8 * * ? *)`

This expression runs at **08:00 every day** in the selected time zone. Verify the **Next 10 trigger dates**, then choose **Next**.

![Configure the daily 08:00 cron schedule](/images/5-Workshop/5.11-eventbridge/schedule-cron.png?featherlight=false&width=90pc)

---

#### Part 2: Select SQS as the Target

**Step 1:** On **Select target**, keep **Templated targets**, then select **Amazon SQS – SendMessage**.

![Select Amazon SQS as the target](/images/5-Workshop/5.11-eventbridge/select-target.png?featherlight=false&width=90pc)

**Step 2:** Select `playwright-task-queue` and enter the following **MessageBody**:

```json
{
  "target_url": "https://google.com",
  "test_script": "test-script.js",
  "triggered_by": "schedule"
}
```

Replace `target_url` and `test_script` with your actual test URL and script, then choose **Next**.

![Configure the queue and message body](/images/5-Workshop/5.11-eventbridge/schedule-message.png?featherlight=false&width=90pc)

---

#### Part 3: Configure Permissions and Create the Schedule

**Step 1:** Under **Settings**, turn on **Enable schedule**. For a recurring schedule, keep **Action after schedule completion** as `NONE`. The default Retry and DLQ settings are sufficient for this workshop.

![Enable the schedule and review its settings](/images/5-Workshop/5.11-eventbridge/schedule-settings.png?featherlight=false&width=90pc)

**Step 2:** Under **Permissions**, select **Create new role for this schedule**. EventBridge Scheduler creates an execution role that can send messages to the selected SQS queue. Keep the default AWS-owned encryption key, then choose **Next**.

![Create an execution role for Scheduler](/images/5-Workshop/5.11-eventbridge/schedule-role.png?featherlight=false&width=90pc)

**Step 3:** On **Review and create schedule**, verify the name, time zone, cron expression, SQS target, and execution role. Choose **Create schedule**.

![Review the schedule configuration](/images/5-Workshop/5.11-eventbridge/schedule-review.png?featherlight=false&width=90pc)

**Step 4:** Confirm that the new schedule has the **Enabled** status. Review its cron expression, target, and retry policy on the details page.

![The EventBridge schedule is created and enabled](/images/5-Workshop/5.11-eventbridge/schedule-created.png?featherlight=false&width=90pc)

---

#### Verification

- Confirm that the schedule is **Enabled** and its next invocation matches the `Asia/Saigon` time zone.
- At the scheduled time, monitor `playwright-task-queue` in SQS and confirm that a message is delivered.
- Check the CloudWatch Logs for the SQS/ECS processing component and confirm that the test starts with the expected `target_url` and `test_script`.
- To test immediately, temporarily set the schedule to a near-future time, verify the result, and then restore the daily schedule.

{{% notice warning %}}
EventBridge Scheduler cron expressions contain six fields. You cannot specify concrete values for both **day-of-month** and **day-of-week**; use `?` for one of these fields.
{{% /notice %}}

---

Next, proceed to **[5.12. End-to-End Test](../5.12-end-to-end-test/)** to verify the complete system flow.
