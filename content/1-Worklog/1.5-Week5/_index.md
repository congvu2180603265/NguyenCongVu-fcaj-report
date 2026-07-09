---
title: "Week 5 Worklog"
date: 2026-05-15
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---
### Week Objective:
Build a practical understanding of AWS security by concentrating on three essentials: IAM-based access control, encryption with KMS, and basic monitoring with CloudWatch.

### Work completed during the week:
| Day | Date | Focus | Notes |
|---|---|---|---|
| Friday | 05/15/2026 | Security overview | Reviewed Module 05 and summarized the relationship between permissions, identity-based access, and encryption keys | YouTube (FCJ playlist) |
| Saturday | 05/16/2026 | IAM Role for EC2 | Completed Lab 48 by comparing two access patterns for S3 from EC2: direct Access Key usage and an attached IAM Role | awsstudygroup.com (Lab 48) |
| Sunday | 05/17/2026 | IAM review | Reworked Lab 48 once more and documented why embedding Access Keys in application code is a bad practice | awsstudygroup.com (Lab 48) |
| Monday | 05/18/2026 | KMS and S3 encryption | Followed Lab 33 to create a KMS Key, enable S3 object encryption, and verify that access is enforced by encryption permissions | awsstudygroup.com (Lab 33) |
| Tuesday | 05/19/2026 | KMS permission test | Rechecked the Lab 33 setup by confirming that S3 access alone was not enough when the user lacked permission on the KMS Key | awsstudygroup.com (Lab 33) |
| Wednesday | 05/20/2026 | CloudWatch basics | Practiced Lab 08 and set up a simple monitoring flow with Metrics, Logs, Alarm, and Dashboard | awsstudygroup.com (Lab 8) |
| Thursday | 05/21/2026 | Planning ahead | Looked ahead to Module 06 on databases and deliberately deferred advanced IAM labs to keep the schedule focused | YouTube (FCJ playlist) |

### Week 5 Achievements:
* Clarified the role of IAM Roles and the risks of using long-lived Access Keys in applications.
* Finished Lab 48 and successfully used an IAM Role on EC2 for safer S3 access.
* Finished Lab 33 and verified how AWS KMS adds an extra layer of protection for encrypted objects.
* Finished Lab 08 and gained a working understanding of CloudWatch Metrics, Logs, Alarms, and Dashboards.
* Kept the workload under control by postponing advanced IAM labs and reserving time for the database module.
