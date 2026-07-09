---
title: "AI-Powered Test Automation: A Breakthrough from Amazon Bedrock and Rapise"
date: 2026-06-13
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

If you're running large software projects, automated testing is definitely no small pain point. The application UI changes constantly, and element selectors/IDs shift with every update. The result is that QA engineers spend hours just "fixing test code" (script maintenance) instead of writing new test scripts.

To fully solve this problem, AWS has put forward a strategic answer: integrating the AI power of Amazon Bedrock into the Rapise automated testing tool (from partner Inflectra).

Does this combination really free up QA teams' workload? Let's take an objective look at this solution.

### The Gap in Traditional Test Automation

Most traditional testing frameworks operate on a "rigid" mechanism: finding elements on a web page based on a fixed ID or XPath. The moment the frontend team makes even a small change to the UI structure:

* Test scripts break immediately (broken scripts).
* The system reports false errors (false positives), adding noise to CI/CD results.
* Script maintenance costs spike, slowing down product release velocity.

Organizations don't just need a script recorder, they need a testing system capable of self-awareness and adaptation.

### How the "AI Brain + Testing Tool" Duo Operates

This integrated solution uses large language models (LLMs) through Amazon Bedrock as the "brain" analyzing for the Rapise tool. The operating process consists of 3 closed-loop steps:

* **UI Contextual Awareness:** Instead of only looking at raw HTML code, Amazon Bedrock helps Rapise "see" and understand the interface like a human would (recognizing which element is a button, which is an input field, based on context).
* **Self-Healing Scripts:** When the application updates and changes the UI structure, the AI automatically analyzes and finds the most suitable replacement element so the test can continue running, without an engineer needing to manually fix the code.
* **AI-Driven Generation:** Just describe a test in natural language (e.g., "Test the add-to-cart feature"), and the AI combined with Rapise will automatically propose and build the corresponding test steps.

### A 5-Step Closed-Loop Rollout Plan (Theory vs. Reality)

* **Step 1: Monitor (System assessment)** Re-evaluate all existing test scripts, identify UI areas that change frequently, to prepare for applying AI.
* **Step 2: Detect (Infrastructure connection)** Set up API access connecting the Rapise tool and the Amazon Bedrock service within a secure AWS Cloud environment.
* **Step 3: Investigate (Context training)** Have the AI scan through the application to create a baseline of UI elements, helping the system deeply understand the app's flow.
* **Step 4: Remediate (Enable auto-fix)** Turn on the Self-Healing feature. When running tests in the CI/CD pipeline, if a UI change causes an error, the AI automatically "patches" the script in real time and sends a report to the system.
* **Step 5: Guard (Ongoing governance)** Continuously optimize Amazon Bedrock token usage costs and keep updating test scripts to match the product's new features.

### Closing Thoughts: An Objective View

The combination of Amazon Bedrock and Rapise is a major step forward for the enterprise customer segment, where source data security and system stability are top priorities.

However, since this is a solution within a closed ecosystem (Rapise's paid commercial model plus AWS Cloud infrastructure), it will best suit large organizations that already have AWS infrastructure in place, rather than small startups that prioritize open-source tools. Still, overall, the era of "AI-powered test automation" has truly begun and will soon completely change how we do software QA.

Original article link: [aws.amazon.com/blogs/apn/ai-powered-test-automation-with-rapise-and-amazon-bedrock](https://aws.amazon.com/vi/blogs/apn/ai-powered-test-automation-with-rapise-and-amazon-bedrock/)

{{% notice tip %}}
Screenshot of this post shared on the AWS Study Group Facebook group here.
{{% /notice %}}
![Facebook post screenshot](/images/anhblog2.png)

#AWS #AmazonBedrock #Rapise #AutomationTest #QA #DevSecOps