---
title: "AWS Transform – When AI Automatically Cleans Up Technical Debt Across Thousands of Repositories"
date: 2026-06-20
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

AWS has just launched a new feature within AWS Transform: Continuous Modernization (currently in preview), essentially a tool that automatically scans, detects, and fixes technical debt across an organization's entire codebase, without humans having to manually handle each repo.

### The Problem It Solves

Companies often spend up to 30% of their IT budget just maintaining legacy systems. A common situation is using a patchwork of disconnected tools: one tool detects outdated dependencies, another checks vulnerabilities, another checks code quality, but nothing ties it all together and automatically fixes things at scale.

The result: engineering teams get bogged down cleaning up instead of building new features. And ironically, AI coding agents make this situation worse — code gets generated faster, but debt piles up faster too.

### How It Works

There are two modes:

* **Continuous mode** — continuously scans the entire repository against predefined policies (or the organization's own custom policies). When it detects a repo that has fallen behind the baseline, it automatically creates a pull request to fix it and notifies that team. The team just needs to review and merge.
* **Campaign mode** — for larger modernization projects, such as migrating a framework or upgrading a major version across hundreds of apps at once.

Out of the box, it already supports: upgrading the Java version, upgrading Node.js, migrating the AWS SDK, and updating Lambda runtimes before they lose support.

### What's Interesting

It integrates with the AWS Security Agent, meaning source-code-level security vulnerabilities are brought into the same detection-and-fix pipeline, instead of being a separate tool.

It can also be used via MCP, meaning it can be integrated into an organization's existing coding agents.

Original article link: [aws.amazon.com/blogs/aws/proactively-reduce-tech-debt-autonomously-with-aws-transform-continuous-modernization-preview](https://aws.amazon.com/vi/blogs/aws/proactively-reduce-tech-debt-autonomously-with-aws-transform-continuous-modernization-preview)

{{% notice tip %}}
Screenshot of this post shared on the AWS Study Group Facebook group here.
{{% /notice %}}
![Facebook post screenshot](/images/anhblog3.png)

#AWS #AWSTransform #TechnicalDebt #DevOps #AIAgent #CloudModernization