---
title: "Week 8 Worklog"
date: 2026-06-05
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objective:
Week 8 marked a shift from completing individual labs to thinking more like a product builder. I started working with the team on project ideas while independently exploring areas that might connect to my role, such as automated testing, Docker, and storing test data with DynamoDB.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 06/05/2026 | Reviewed DynamoDB and Boto3 from an application-design perspective: if a system generates logs or test history, what keys should be used and how should the records be queried later | docs.aws.amazon.com/dynamodb |
| Saturday | 06/06/2026 | Read selected AWS blog posts to see how serverless architectures are applied to real problems, then collected ideas that could be adapted for the team project | aws.amazon.com/blogs |
| Sunday | 06/07/2026 | Rechecked networking and security topics, especially common risk points when multiple services communicate with each other, such as permissions, endpoints, and network configuration | Personal notes |
| Monday | 06/08/2026 | Discussed possible project directions with the team and filtered ideas based on feasibility, required AWS services, and whether the result could become a useful blog topic | aws.amazon.com/blogs |
| Tuesday | 06/09/2026 | Started learning Playwright: how it simulates user actions, runs automated tests, and can be paired with Docker to create a stable testing environment | playwright.dev |
| Wednesday | 06/10/2026 | Drafted a DynamoDB flow for storing test results, including test cases, execution status, runtime, and error details when a test fails | docs.aws.amazon.com/dynamodb |
| Thursday | 06/11/2026 | Consolidated technical notes, identified the part I could reasonably take ownership of, and prepared input for the team before finalizing the implementation direction | Personal notes |

### Week 8 Achievements:
* Started moving from isolated lab practice toward thinking about a multi-component system.
* Clarified a likely contribution area: DynamoDB for test/log data and support for automated testing workflows.
* Understood the basic role of Playwright and Docker in building repeatable test execution.
* Took part in project idea selection and helped prepare the direction for the team blog post.
* Identified implementation areas that would need extra care, especially networking and security.
