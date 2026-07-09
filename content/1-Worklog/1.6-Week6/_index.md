---
title: "Week 6 Worklog"
date: 2026-05-22
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Week 6 Objective:
Focus deeply on Module 06 about databases, especially Amazon RDS and DynamoDB because this is one of my stronger areas. I spent more time practicing these parts carefully than the remaining topics.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 05/22/2026 | Watched the Module 06 lecture and read in-depth documentation about Amazon RDS and Aurora; noted common database engines such as MySQL, PostgreSQL, and Aurora, along with concepts like Multi-AZ and Read Replica | docs.aws.amazon.com/rds |
| Saturday | 05/23/2026 | Practiced Lab 05 with Amazon RDS: created an RDS MySQL database, configured the Security Group so EC2 could connect to it, installed Node.js and the MySQL client, then deployed a Node.js application; fixed an empty `.env` file issue and a package compatibility issue by switching from `mysql` to `mysql2`, allowing the application to run and connect successfully to RDS | awsstudygroup.com (Lab 5) |
| Sunday | 05/24/2026 | Reviewed SQL from basic to advanced levels, especially JOIN, GROUP BY, subqueries, and indexes, to use SQL more effectively when working with and optimizing data |  |
| Monday | 05/25/2026 | Studied Amazon DynamoDB in depth, including the NoSQL model, Partition Key, Sort Key, and table design practices for efficient queries and avoiding hot partitions | docs.aws.amazon.com/dynamodb |
| Tuesday | 05/26/2026 | Practiced Lab 60 about DynamoDB: created a table using AWS CLI, performed full CRUD operations, queried data using Query and Scan, and clearly distinguished the difference between these two query methods | awsstudygroup.com (Lab 60) |
| Wednesday | 05/27/2026 | Continued Lab 60 with a Global Secondary Index (GSI) to query by a non-primary-key attribute, then repeated the full workflow using the Python SDK Boto3 | awsstudygroup.com (Lab 60) |
| Thursday | 05/28/2026 | Wrote an additional Boto3 script to create a table, insert sample data, query records, and delete items; this directly prepared me for the DynamoDB part I would be responsible for in the team project |  |

### Week 6 Achievements:
* Built a solid foundation in AWS database services and clearly distinguished relational models such as RDS/Aurora from NoSQL models such as DynamoDB.
* Completed Lab 05: deployed a Node.js application connected to RDS MySQL and independently fixed the `.env` issue and package compatibility problem by switching to `mysql2`.
* Completed Lab 60: understood table design, CRUD operations, Query/Scan usage, and Global Secondary Index creation in DynamoDB.
* Worked with DynamoDB using both AWS CLI and the Python SDK Boto3.
* Reinforced SQL knowledge from basic to advanced levels to better support hands-on database practice.
* Built a strong enough foundation for the DynamoDB responsibilities in the team project.
