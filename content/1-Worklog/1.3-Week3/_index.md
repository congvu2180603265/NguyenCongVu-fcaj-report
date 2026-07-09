---
title: "Week 3 Worklog"
date: 2026-05-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Week Objective:
Focus on the more advanced parts of Module 02, especially DNS Resolution and VPC Peering. Because the networking material is challenging, I concentrated on a few core labs and worked through them carefully.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 05/01/2026 | Reviewed the instructions for Lab 10 (DNS Resolution) and Lab 19 (VPC Peering), and took notes on the prerequisites and steps needed before starting the labs | YouTube (FCJ playlist) |
| Saturday | 05/02/2026 | Revisited both labs in detail and studied Route 53 Resolver, Inbound Endpoints, and Private Hosted Zones so the hands-on part would be easier to follow |  |
| Sunday | 05/03/2026 | Started Lab 10; the provided CloudFormation template was failing, so I researched how to create the Inbound Endpoint manually instead of relying on the ready-made script | awsstudygroup.com (Lab 10) |
| Monday | 05/04/2026 | Continued Lab 10 by setting up the Private Hosted Zone and associating it with the VPC to complete internal DNS resolution | awsstudygroup.com (Lab 10) |
| Tuesday | 05/05/2026 | Finished Lab 10 and read additional material to fully understand how internal domain name resolution works inside a VPC | awsstudygroup.com (Lab 10) |
| Wednesday | 05/06/2026 | Practiced Lab 19 on VPC Peering; two EC2 instances in separate VPCs could not ping each other at first, so I used traceroute to trace the issue, found the problem in the Route Table, and fixed it successfully | awsstudygroup.com (Lab 19) |
| Thursday | 05/07/2026 | Reviewed all networking topics covered so far and decided to pause Lab 20 (Transit Gateway) for now in order to focus on the next modules |  |

### Week 3 Achievements:
* Completed Lab 10 on DNS Resolution and Lab 19 on VPC Peering.
* Learned how to work around a broken CloudFormation template by configuring the Inbound Endpoint and Private Hosted Zone manually.
* Gained a clearer understanding of how internal DNS resolution works inside a VPC.
* Used traceroute to diagnose and fix routing issues in a VPC Peering setup.
* Improved my ability to research and troubleshoot when the lab instructions did not work as expected.
