---
title: "FCAJ Community Day"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Reflection Report: FCAJ Community Day

### Event Information

**FCAJ Community Day** was part of the AWS First Cloud AI Journey program. It was held on Saturday morning, May 23, 2026, on the 26th floor of Bitexco Financial Tower in Ho Chi Minh City.

The event took place from 9:00 AM to 12:00 PM and included multiple sharing sessions about AI, cloud, CloudFront, Amazon QuickSight, LLM behavior, multi-agent systems, and real-world product-building experience.

### Purpose of Attending

My goal in attending this event was to broaden my understanding of how AI and cloud are applied in real projects, not only from theory but also from the experience of speakers working in the industry. It was also a chance to connect with the FCAJ community, learn career insights, and understand how technology products can be built with practical value.

### Main Event Content

#### Context Is Everything: Making AI Actually Work for You

Tinh Truong's session helped me understand that AI performance does not depend only on powerful models or good prompts. It also depends heavily on context. Without enough context, AI can easily produce generic, inaccurate, or irrelevant answers.

The most impressive idea for me was the shift from isolated prompts to systems with memory and long-term context. This made me see AI as a tool that needs thoughtful workflow design instead of random question-and-answer usage.

#### Friendly AI Assistant with Amazon Quick

Anh Pham's session introduced how AI tools can support data exploration, workflow creation, and report building through natural language. The discussion about Quick Chat Agent, Quick Flows, Quick Spaces, and QuickSight showed how AI can become part of everyday data analysis work.

From this session, I learned that AI is not only useful for generating content. It can also help users explore data, automate tasks, and turn individual insights into shared team knowledge.

#### From Edge To Origin: CloudFront as Your Foundation

Thinh Nguyen's session focused on Amazon CloudFront and the role of edge networks in improving performance, reducing cost, increasing reliability, and strengthening application security.

Before the event, I mainly thought of CloudFront as a caching layer for static content. After this session, I understood that CloudFront can become a foundation layer for many workloads, especially when a system needs better user experience, origin protection, and traffic control.

#### 36 hrs with LotusHacks: Building UTMorpho from Idea to Reality

Team VIB's sharing gave me a realistic view of product development during a hackathon. The team explained how they formed the idea, defined the problem, built under a 36-hour time limit, faced issues, adjusted direction, and completed the demo.

The key lesson for me was that when time is limited, a team must focus on the core problem, divide tasks clearly, and test the product continuously instead of trying to build too many features at once.

#### Non-Determinism of "Deterministic" LLM Settings

Duc Dao's session helped me better understand how LLMs choose the next token and why settings such as temperature = 0 do not always guarantee identical outputs.

This topic is important because AI-powered systems should not treat AI as a fully deterministic function. Instead, they need testing, logging, guardrails, and risk-reduction strategies to make the system more stable.

#### Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring

Vy Lam's session showed how a multi-agent system can be applied in an enterprise problem, specifically startup credit scoring. The discussion about single agents, the multi-agent paradigm, a virtual credit committee, guardrails, and compliance showed that enterprise AI needs more control layers than a simple demo.

I paid special attention to guardrails and compliance because they are critical when AI is used in high-risk workflows such as finance, sensitive data processing, or business decision-making.

### Knowledge and Lessons Learned

* AI needs good context, relevant data, and a clearly designed workflow to work effectively.
* CloudFront is not only a CDN for static files, but also a layer for security, cost optimization, performance, and reliability.
* AI tools for data analysis can reduce the gap between business users and technical systems.
* When building a product under time pressure, the team should focus on the core problem, demonstrate the main flow, and iterate quickly.
* LLMs have a certain level of non-determinism, so AI systems need logging, testing, and output-control mechanisms.
* Enterprise multi-agent systems require clear agent roles, input data control, guardrails, compliance, and long-term operational planning.

### Connection to My Team Project

This event was directly related to my team project, especially the Post-processing & AI part that I am responsible for. After listening to the sessions about context, LLM behavior, and multi-agent systems, I realized that calling an AI API to summarize errors should not simply mean sending raw logs.

Instead, the post-processing Lambda should prepare better context: test case information, key errors, execution time, important logs, system status, and related data from DynamoDB. The report stored in S3 should also have a clear structure so users can understand the error cause and suggested next steps.

The CloudFront and security sessions also helped me review the team architecture from a safer perspective: avoid making unnecessary resources public, use Presigned URLs for reports, use Secrets Manager for API keys, and use VPC Endpoints for internal AWS service access.

### Personal Reflection

FCAJ Community Day was very valuable to me because the content was not limited to one AWS service. It expanded my thinking about how to build AI and cloud products in a real-world context. I appreciated how the speakers combined technical knowledge, practical experience, and career guidance for students.

After the event, I felt more motivated to improve the team project in a practical direction: not only making the demo run, but also paying attention to security, cost, user experience, report quality, and the way AI is integrated into the system.
