---
title: "Week 2 Worklog"
date: 2026-04-24
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---
### Week Objective:
Start Module 02 on virtual networking (VPC) and move through each part slowly to understand how the network components work together.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 04/24/2026 | Watched the Module 02 lecture about Amazon VPC and noted the overall network architecture as well as the role of each component, including VPC, Subnet, Gateway, and Route Table | awsstudygroup.com (Lab 3) |
| Saturday | 04/25/2026 | Worked through Lab 3 for VPC and Subnet creation; followed the material closely to understand CIDR allocation and the difference between Public and Private Subnets | awsstudygroup.com (Lab 3) |
| Sunday | 04/26/2026 | Continued Lab 3 with Internet Gateway and Route Table setup, configured routing so the Public Subnet could reach the internet, and reviewed the VPC security section | awsstudygroup.com (Lab 3) |
| Monday | 04/27/2026 | Configured Security Groups for Public and Private resources; at first inbound and outbound rules were confusing, so the rules were tested and adjusted multiple times until they were clear | awsstudygroup.com (Lab 3) |
| Tuesday | 04/28/2026 | Spent time reviewing all VPC components again and redrew the network diagram on paper to better remember how the pieces connect |  |
| Wednesday | 04/29/2026 | Practiced deploying EC2 inside a VPC and configured an EC2 Instance Connect Endpoint to access the instance in the Private Subnet securely | awsstudygroup.com (Lab 3) |
| Thursday | 04/30/2026 | Tested network connectivity between EC2 instances in the VPC, confirmed that the Public and Private setups worked as designed, and wrapped up the basic part of Module 02 |  |

### Week 2 Achievements:
* Understood and configured the core VPC components: Subnet, Internet Gateway, Route Table, and Security Group.
* Distinguished between Public Subnet and Private Subnet, and understood how traffic is routed out to the internet.
* Successfully deployed EC2 inside a VPC and used Instance Connect Endpoint to securely access a Private instance.
* Overcame the initial confusion between inbound and outbound Security Group rules.
* Built a stronger networking foundation for the more advanced labs in the following week.
