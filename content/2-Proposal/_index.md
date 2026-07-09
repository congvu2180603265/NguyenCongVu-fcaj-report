---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# IoT Weather Platform for Lab Research
## A Unified AWS Serverless Solution for Real-Time Weather Monitoring

### 1. Executive Summary

The IoT Weather Platform is designed for the ITea Lab team in Ho Chi Minh City to improve weather data collection, storage, and analysis. The system initially supports 5 weather stations and can scale to 10-15 stations, using Raspberry Pi edge devices with ESP32 sensors to send data through MQTT.

The platform uses AWS Serverless services to provide real-time monitoring, trend analysis, and cost-efficient operations. Access is limited to 5 lab members through Amazon Cognito.

### 2. Problem Statement

#### Current Problem

The existing weather stations require manual data collection, which becomes difficult as the number of stations increases. Data is not centralized, real-time monitoring is limited, and there is no unified analytics platform. Third-party platforms such as ThingsBoard or CoreIoT offer many features, but they may be more complex and costly than what the lab needs for internal research.

#### Proposed Solution

The platform uses AWS IoT Core to ingest MQTT data from weather stations. Raw data is stored in Amazon S3 as a data lake, then AWS Glue Crawlers and ETL jobs catalog, transform, and load the data into another S3 bucket for analysis. AWS Lambda and Amazon API Gateway support processing and communication with the web application. The interface is built with Next.js and deployed through AWS Amplify, while Amazon Cognito handles user authentication.

The solution still supports important features such as device registration, connection management, real-time dashboards, and trend analysis, but it is designed at a smaller scale to match the lab's internal research needs.

#### Benefits and Return on Investment

The solution reduces manual data collection and reporting, improves data reliability, and creates a foundation for future AI research. Structured weather data can be used for model training, trend analysis, or predictive features.

Estimated AWS operating cost is about $0.70 per month, equivalent to $8.40 for 12 months. Hardware cost is estimated at a one-time $265 for Raspberry Pi 5 and sensors. The expected break-even period is 6-12 months due to operational time savings and reduced manual work.

### 3. Solution Architecture

The platform applies a serverless AWS architecture to manage data from Raspberry Pi-based weather stations. Data is sent through MQTT to AWS IoT Core, stored in an S3 data lake, and processed with AWS Glue for analysis. Lambda and API Gateway provide the required APIs, while Amplify hosts the Cognito-protected Next.js dashboard.

![IoT Weather Station Architecture](/images/proposal-edge-architecture.jpeg)

![IoT Weather Platform Architecture](/images/proposal-platform-architecture.jpeg)

### AWS Services Used

- **AWS IoT Core**: Ingests MQTT data from 5 stations, scalable to 15 stations.
- **AWS Lambda**: Processes data and triggers Glue jobs.
- **Amazon API Gateway**: Provides APIs for the web application.
- **Amazon S3**: Stores raw data in a data lake and processed data in a separate bucket.
- **AWS Glue**: Crawlers catalog data; ETL jobs transform and load data.
- **AWS Amplify**: Deploys the Next.js web interface.
- **Amazon Cognito**: Manages authentication and limits access for lab users.

### Component Design

- **Edge Devices**: Raspberry Pi collects and filters sensor data, then sends it to IoT Core.
- **Data Ingestion**: AWS IoT Core receives MQTT messages from edge devices.
- **Data Storage**: Raw data is stored in an S3 data lake; processed data is stored in another S3 bucket.
- **Data Processing**: AWS Glue Crawlers catalog the data; ETL jobs transform it for analysis.
- **Web Interface**: AWS Amplify deploys the Next.js app for dashboards and real-time analytics.
- **User Management**: Amazon Cognito manages up to 5 active accounts.

### 4. Technical Implementation

#### Implementation Phases

The project has two main parts: setting up weather edge stations and building the weather platform on AWS.

- **Phase 1 - Research and Architecture Design**: Study Raspberry Pi, ESP32, weather sensors, and design the AWS Serverless architecture.
- **Phase 2 - Cost Estimation and Feasibility Check**: Use AWS Pricing Calculator to estimate costs and adjust the project scope.
- **Phase 3 - Architecture Optimization**: Refine the design to match cost, usage needs, and deployment feasibility.
- **Phase 4 - Development, Testing, and Deployment**: Program the edge devices, configure AWS services with CDK/SDK, and build the Next.js application.

#### Technical Requirements

- **Weather Edge Station**: Temperature, humidity, rainfall, and wind speed sensors; ESP32; Raspberry Pi running Raspbian and Docker to filter data before sending it through MQTT.
- **Weather Platform**: AWS Amplify, Lambda, AWS Glue, S3, IoT Core, API Gateway, and Cognito.
- **Development Tools**: AWS CDK/SDK for infrastructure and service integration; Next.js for the fullstack web application.

### 5. Timeline and Milestones

- **Pre-Internship (Month 0)**: Plan the project, review the existing weather station, and prepare the initial architecture.
- **Month 1**: Study related AWS services and upgrade hardware.
- **Month 2**: Design, adjust the architecture, and check costs.
- **Month 3**: Implement, test, and launch the system.
- **Post-Launch**: Maintain data for up to 1 year for research.

### 6. Budget Estimation

You can view the cost estimate on the [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01).  
Or download the [Budget Estimation File](../attachments/budget_estimation.pdf).

### Infrastructure Costs

- AWS Lambda: $0.00/month (1,000 requests, 512 MB storage).
- S3 Standard: $0.15/month (6 GB, 2,100 requests, 1 GB scanned).
- Data Transfer: $0.02/month (1 GB inbound, 1 GB outbound).
- AWS Amplify: $0.35/month (256 MB, 500 ms requests).
- Amazon API Gateway: $0.01/month (2,000 requests).
- AWS Glue ETL Jobs: $0.02/month (2 DPUs).
- AWS Glue Crawlers: $0.07/month (1 crawler).
- MQTT (IoT Core): $0.08/month (5 devices, 45,000 messages).

**Total AWS cost**: $0.70/month, equivalent to $8.40 for 12 months.  
**Hardware cost**: $265 one-time for Raspberry Pi 5 and sensors.

### 7. Risk Assessment

#### Risk Matrix

- **Network outages**: Medium impact, medium probability.
- **Sensor failures**: High impact, low probability.
- **Cost overruns**: Medium impact, low probability.

#### Mitigation Strategies

- **Network**: Store data temporarily on Raspberry Pi when the connection is unavailable.
- **Sensors**: Perform regular checks and prepare spare components.
- **Cost**: Configure AWS Budgets and optimize resources based on actual usage.

#### Contingency Plans

- Return to manual collection if the AWS system has a major failure.
- Use CloudFormation/CDK to restore infrastructure configuration when needed.

### 8. Expected Outcomes

#### Technical Improvements

The system replaces manual collection with real-time monitoring, centralized storage, and AWS-based data analysis. The architecture can scale from 5 stations to 10-15 stations.

#### Long-Term Value

The platform creates a 1-year weather dataset for AI research and can be reused as a foundation for future IoT or data analytics projects.
