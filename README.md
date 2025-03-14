# ResilientAI Infrastructure

In 2025, there is a growing trend of moving away from **global AI cloud services** to **open-source models** that can be deployed locally. This shift is driven by the need to keep **data safe**, reduce costs, and accelerate the development and operational speed of AI systems. Open-source models provide the flexibility to run on local infrastructure, which allows organizations to have full control over their data and computational resources. However, the transition back to local services introduces a challenge: cloud services were initially designed to solve **availability issues** that arose in the 90s when servers would crash or fail due to hardware issues, electricity outages, or other disruptions. To address these challenges, we propose an infrastructure that combines the best of both worlds, using **local resources for cost efficiency** and **cloud failover mechanisms** for high availability and business continuity.

This project leverages a **hybrid infrastructure** to provide a **cost-efficient**, **scalable**, and **reliable solution** for managing public websites, admin panels, and AI-powered services. The infrastructure is designed to seamlessly fall back to cloud services during **electricity outages** or other interruptions in local services, ensuring continuous operation without downtime.

## Table of Contents

- [ResilientAI Infrastructure](#resilientai-infrastructure)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Goal](#goal)
    - [Cost and Speed Comparison](#cost-and-speed-comparison)
  - [Comprehensive Cost Estimation](#comprehensive-cost-estimation)
  - [Detailed Cost and Scaling Information](#detailed-cost-and-scaling-information)
  - [Specific AI Service Costs](#specific-ai-service-costs)
  - [Country-Specific Electricity Costs](#country-specific-electricity-costs)
  - [Use Case Scenario](#use-case-scenario)
  - [Scaling Considerations for 2025](#scaling-considerations-for-2025)
  - [Architecture](#architecture)
  - [Technologies](#technologies)
  - [Service Breakdown](#service-breakdown)
    - [Public Website (Next.js)](#public-website-nextjs)
    - [Admin Panel (Next.js)](#admin-panel-nextjs)
    - [AI Bot Services (Python)](#ai-bot-services-python)
    - [PostgreSQL Databases](#postgresql-databases)
  - [CI/CD and AWS Integration](#cicd-and-aws-integration)
    - [CI/CD Pipeline](#cicd-pipeline)
    - [AWS Integration](#aws-integration)
  - [Local Development](#local-development)
  - [Deployment](#deployment)
  - [End-to-End (E2E) Testing](#end-to-end-e2e-testing)
    - [Overview](#overview)
    - [Cypress Setup](#cypress-setup)
    - [Running the Tests](#running-the-tests)
  - [Failover Testing](#failover-testing)
    - [Overview](#overview-1)
    - [Automation in CI/CD](#automation-in-cicd)
    - [Contributing](#contributing)
  - [Diagram](#diagram)
  - [Development Workflow with GitFlow](#development-workflow-with-gitflow)

## Introduction

This project is designed to handle both local and cloud infrastructure efficiently, using **Docker** for containerization, **AWS** for cloud deployment, and a **hybrid model** that falls back to the cloud during interruptions. The system is optimized for **cost-efficiency** while still ensuring **high availability** by running the **AI services locally** unless there is an interruption.

- **Public Website**: A **static, SEO-optimized** website built with **Next.js**.
- **Admin Panel**: A **dynamic, interactive** admin panel also built with **Next.js**.
- **AI Services**: AI models run **locally** on the server for **cost efficiency**, but **switch to the cloud** (AWS) when local resources are unavailable (e.g., electricity outages).
- **PostgreSQL Databases**: Two **PostgreSQL** databases (staging and production) are hosted on **AWS RDS** for reliability, but local copies are maintained for quick failover.

## Goal

The primary goal of this infrastructure is to create a **powerful, reliable, and cost-effective system**:
1. **Cost-Efficiency**: **AI services** can be expensive when running on the cloud. The system is designed to run AI tasks locally on the server to **reduce costs**, switching to **cloud-based services** only when **local infrastructure is unavailable** (e.g., due to power outages).
2. **Reliability**: The system ensures that the services continue to function even if the local server experiences downtime. In case of an **electricity failure**, services will automatically switch to the **cloud** until the local infrastructure is restored.
3. **SEO-Optimized Public Website**: The **public website** should be **static and SEO-optimized** for better performance and search engine rankings.
4. **Dynamic Admin Panel**: The **admin panel** is **dynamic** and allows administrators to interact with the system, manage users, monitor AI bots, and perform other administrative tasks.

### Cost and Speed Comparison

In this section, we compare the costs and speed of using full token AI services (e.g., OpenAI, Google Gemini) versus running personal AI on a VPS and a local server machine. This comparison includes electricity costs and initial investment in a computer, considering a scenario where there is a half-day shutdown of the local machine every two months.

## Comprehensive Cost Estimation

<div style="overflow-x:auto;">
<table style="width: 100%; border-collapse: collapse;">
  <thead>
    <tr>
      <th style="border: 1px solid #ddd; padding: 8px;">Option</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Initial Investment (EUR)</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Monthly Subscription (EUR)</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Monthly Electricity Cost (EUR)</th>
      <th style="border: 1px solid #ddd; padding: 8px;">AI Service Cost Details</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Remarks</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Local Machine Resources</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">Full Token AI Services (Cloud)</td>
      <td style="border: 1px solid #ddd; padding: 8px;">0</td>
      <td style="border: 1px solid #ddd; padding: 8px;">90 – 900</td>
      <td style="border: 1px solid #ddd; padding: 8px;">0</td>
      <td style="border: 1px solid #ddd; padding: 8px;">
        <ul style="margin: 0; padding-left: 20px;">
          <li>Video: €0.09–€0.45/min</li>
          <li>Text: €0.009–€0.045 per 1,000 tokens</li>
          <li>Email: €0.009–€0.027 per email</li>
          <li>Avg: ~€0.105/min</li>
        </ul>
      </td>
      <td style="border: 1px solid #ddd; padding: 8px;">No hardware or electricity cost. On-demand AI charges may add up quickly with high usage.</td>
      <td style="border: 1px solid #ddd; padding: 8px;">N/A</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">Custom AI on VPS</td>
      <td style="border: 1px solid #ddd; padding: 8px;">900 – 2,700</td>
      <td style="border: 1px solid #ddd; padding: 8px;">45 – 180</td>
      <td style="border: 1px solid #ddd; padding: 8px;">18 – 45</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup cost then minimal per-use cost for video, text, and email services</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Balanced option with control and predictable expenses, including minor electricity cost.</td>
      <td style="border: 1px solid #ddd; padding: 8px;">N/A</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">Custom AI on Local Machine</td>
      <td style="border: 1px solid #ddd; padding: 8px;">1,800 – 4,500</td>
      <td style="border: 1px solid #ddd; padding: 8px;">0</td>
      <td style="border: 1px solid #ddd; padding: 8px;">27 – 63</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup cost then minimal per-use cost for video, text, and email services</td>
      <td style="border: 1px solid #ddd; padding: 8px;">High upfront investment with very low recurring costs aside from electricity. Full data control, but review local downtime impacts.</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Electricity Consumption: 27 - 63 EUR/month</td>
    </tr>
  </tbody>
</table>
</div>

## Detailed Cost and Scaling Information

## Specific AI Service Costs

<div style="overflow-x:auto;">
<table style="width: 100%; border-collapse: collapse;">
  <thead>
    <tr>
      <th style="border: 1px solid #ddd; padding: 8px;">Service</th>
      <th style="border: 1px solid #ddd; padding: 8px;">OpenAI/Google Gemini Cost</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Custom AI Agent on VPS Cost</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Custom AI Agent on Local Machine Cost</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Hosting & AI Computing Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;"><strong>Video Creation</strong></td>
      <td style="border: 1px solid #ddd; padding: 8px;">€0.09 - €0.45 per minute</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup and minimal cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup and minimal cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Malta: €0.18/kWh, France: €0.16/kWh</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;"><strong>Text Generation</strong></td>
      <td style="border: 1px solid #ddd; padding: 8px;">€0.009 - €0.045 per 1,000 tokens</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup and minimal cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup and minimal cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Malta: €0.18/kWh, France: €0.16/kWh</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;"><strong>Email Automation</strong></td>
      <td style="border: 1px solid #ddd; padding: 8px;">€0.009 - €0.027 per email</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup and minimal cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Initial setup and minimal cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Malta: €0.18/kWh, France: €0.16/kWh</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;"><strong>Average Total Cost</strong></td>
      <td style="border: 1px solid #ddd; padding: 8px;">€0.105 per minute</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Minimal cost after setup</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Minimal cost after setup</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Malta: €0.18/kWh, France: €0.16/kWh</td>
    </tr>
  </tbody>
</table>
</div>

## Country-Specific Electricity Costs

<ul>
  <li><strong>Malta</strong>: Approx. €0.18 per kWh</li>
  <li><strong>France</strong>: Approx. €0.16 per kWh</li>
</ul>

## Use Case Scenario

In a scenario where the local machine experiences a half-day shutdown every two months, the cost-effectiveness of each option can vary:
<ul>
  <li><strong>Full Token AI Services</strong>: Ideal for those who prioritize speed and seamless operation without worrying about hardware maintenance or electricity costs.</li>
  <li><strong>Personal AI on VPS</strong>: Suitable for those who want full control over their data and are willing to manage their own infrastructure, potentially saving costs in the long run.</li>
  <li><strong>Local Server Machine</strong>: Best for those who need high performance and full data control, with a willingness to handle hardware and maintenance costs.</li>
</ul>

## Scaling Considerations for 2025

<table style="width:100%; border-collapse:collapse;">
  <thead>
    <tr>
      <th style="border: 1px solid #ddd; padding: 8px;">Active Users</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Recommended Hardware Configuration</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Estimated Additional Investment (€/year)</th>
      <th style="border: 1px solid #ddd; padding: 8px;">Performance Note (Speed Equivalence)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">Up to 100</td>
      <td style="border: 1px solid #ddd; padding: 8px;">1 x High-end Mac Studio (e.g., M2 Ultra/M3 Max)</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Base purchase cost</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Near cloud-speed performance for light workloads</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">101 - 500</td>
      <td style="border: 1px solid #ddd; padding: 8px;">2-3 x High-end Machines with load balancing (or equivalent multi-core servers)</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Approx. €1,000 - €3,000 per machine</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Maintains high speed; scales processing to meet increased demand</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">501 - 1000</td>
      <td style="border: 1px solid #ddd; padding: 8px;">4+ Machines or a dedicated server cluster</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Significant investment (e.g., €5,000+ extra per year)</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Performance on par with cloud with optimized load distribution</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 8px;">1000+</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Dedicated server cluster with auto-scaling and redundancy (plus potential cloud augmentation)</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Hybrid model cost escalates to approach cloud pricing</td>
      <td style="border: 1px solid #ddd; padding: 8px;">Achieves equivalent speed only with complex infrastructure and dynamic resource scaling</td>
    </tr>
  </tbody>
</table>

## Architecture

The architecture of this project is built around the **hybrid infrastructure** concept. The components of the architecture include:

1. **Public Website**: Built with **Next.js**, this static site is optimized for **SEO** to ensure fast load times and good rankings in search engines.
2. **Admin Panel**: Built with **Next.js**, the admin panel provides a **dynamic** user interface for administrators to manage the application. This service interacts with the backend and offers features such as:
   - **User Management**: Admins can add, modify, or delete users.
   - **System Monitoring**: Admins can monitor AI bots, their performance, and any logs related to their operation.
3. **AI Bot Services**: The AI bots are primarily designed to run on local machines using **Python** and **machine learning** frameworks like **TensorFlow** or **PyTorch**. They are capable of switching to **AWS cloud** if the local infrastructure fails.
4. **PostgreSQL Databases**: **Staging** and **Production** PostgreSQL databases are hosted on **AWS RDS** for high availability. Local instances of the databases are available for failover in case of outages.

In case of a local server failure (e.g., power outages), the system automatically switches traffic to the **cloud-based services** running on **AWS**, ensuring **continuous operation**.

## Technologies

- **Next.js**: For both the **public website** (static, SEO-optimized) and the **admin panel** (dynamic and interactive).
- **Python**: For **AI Bot Services**, using **TensorFlow**, **PyTorch**, or **scikit-learn** for machine learning tasks.
- **PostgreSQL**: For **data storage**, with **AWS RDS** ensuring high availability and replication.
- **Docker**: To containerize the services and provide a consistent environment for both **local development** and **cloud deployment**.
- **AWS**: To deploy cloud-based services and handle **failover**.
- **Route 53**: For **DNS failover** management, ensuring that traffic is routed to the cloud-based services in case the local server is unavailable.
- **CI/CD**: Automated deployment using **GitHub Actions** or **GitLab CI** for **Docker** container management.

## Service Breakdown

### Public Website (Next.js)

The **public website** is a **static site** built with **Next.js** for **SEO** optimization. It serves users with content that is pre-rendered, ensuring fast load times and excellent search engine rankings.

- **Static Content**: Optimized for fast delivery and SEO.
- **Fast and Scalable**: Built to handle high traffic efficiently.

### Admin Panel (Next.js)

The **admin panel** is built with **Next.js** to provide a dynamic interface for administrators to manage the application. This service interacts with the backend and offers features such as:

- **User Management**: Admins can add, modify, or delete users.
- **System Monitoring**: Admins can monitor the AI bots, their performance, and any logs related to their operation.
- **Content Management**: Allows admins to control the data available in the public website.

### AI Bot Services (Python)

The **AI Bot services** are built using **Python** and are optimized to run locally on your server. This provides a **cost-effective solution** since running AI models on cloud-based resources can be expensive. The system will:

- Run **AI services** locally on the server as long as the local infrastructure is available.
- **Switch to cloud-based services** if the local server goes down (e.g., due to electricity outages).
  
Using **Docker**, these services are containerized, making them easy to deploy, test, and manage.

### PostgreSQL Databases

The **PostgreSQL databases** (staging and production) are managed using **AWS RDS** for high availability. Local copies are maintained for quick failover in case of outages.

- **Production Database**: The primary database for the live environment.
- **Staging Database**: Used for testing and validating changes before they go to production.

Both databases are replicated and provide automatic failover when the local server is unavailable.

## CI/CD and AWS Integration

### CI/CD Pipeline

1. **Code Changes**: Developers push changes to the repository, triggering the CI/CD pipeline (e.g., GitHub Actions).
2. **Build Docker Images**: The Docker images for the **web**, **admin**, and **AI bot services** are built and pushed to **Amazon ECR** (Elastic Container Registry).
3. **Deploy to ECS**: The Docker images are deployed to **Amazon ECS** (Elastic Container Service) for cloud-based operations.
4. **Automated Database Migrations**: Migrations are applied automatically to both the **staging** and **production** databases.

### AWS Integration

- **Elastic Load Balancer (ELB)**: Routes traffic to the active servers, whether local or cloud-based.
- **Route 53**: Handles DNS failover, automatically switching traffic from the local server to the cloud-based infrastructure if the local server fails.
- **RDS Multi-AZ**: Provides high availability and automatic failover for **PostgreSQL** databases in the cloud.

## Local Development

For local development, **Docker** is used to containerize all services (public website, admin panel, AI bots, and PostgreSQL databases). This ensures a consistent development environment regardless of the underlying infrastructure.

- **Docker Compose** is used to define and manage all services locally.
- Local instances of **PostgreSQL** (staging and production) are included in the Docker setup for testing.

## Deployment

1. **CI/CD**: The CI/CD pipeline builds, tests, and deploys Docker images to **Amazon ECS** or **EC2**.
2. **Failover**: In case of a local server failure, **Route 53 DNS** automatically switches traffic to cloud-based services running on **AWS ECS**.
3. **PostgreSQL Failover**: If the local database fails, the system will automatically failover to the cloud-based **PostgreSQL** instances on **AWS RDS**.

## End-to-End (E2E) Testing

### Overview

End-to-end (E2E) testing ensures the full system works correctly from the front-end (public website and admin panel) to the backend (AI services and databases). We use **Cypress** for testing the **web interfaces** (public website and admin panel) and simulate real-world scenarios, including failovers.

### Cypress Setup

1. **Install Cypress**: First, install Cypress as a development dependency:

   ```bash
   npm install --save-dev cypress
   ```

2. **Public Website Test**: Create a test file under `cypress/integration/public-website.spec.js` to check the basic functionality of the public website.

```bash
describe('Public Website', () => {
  it('should load the homepage', () => {
    cy.visit('http://localhost:3000'); // Change to your local/public URL
    cy.get('h1').should('contain', 'Welcome to the Public Website');
  });
  it('should load SEO metadata', () => {
    cy.get('meta[name="description"]').should('have.attr', 'content').and('not.be.empty');
  });
});
```

3. **Admin Panel Test**: Create another test file for the **Admin Panel** (`admin-panel.spec.js`):

```bash
describe('Admin Panel', () => {
  it('should load the admin panel', () => {
    cy.visit('http://localhost:5000'); // Change to your admin panel URL
    cy.get('h1').should('contain', 'Admin Panel');
  });
  it('should allow user management', () => {
    cy.get('button').contains('Add User').click();
    cy.get('input[name="username"]').type('newuser');
    cy.get('button').contains('Submit').click();
    cy.get('.user-list').should('contain', 'newuser');
  });
});
```

### Running the Tests

To run the tests, execute the following:

```bash
npx cypress open
```

Cypress will open a browser window where you can see the tests run in real-time.

## Failover Testing

### Overview

Failover testing ensures the robustness and reliability of the infrastructure when switching from local to cloud resources (and vice versa).

1.  **Simulate Local Server Failure**:
    -   Use **Docker Compose** to stop local services or simulate a power failure.
    -   Monitor if **Route 53 DNS** automatically reroutes traffic to cloud-based services hosted on **AWS ECS/EC2**.
2.  **Test DNS Failover**:
    -   Ensure that **Route 53 DNS** health checks correctly route traffic to the cloud-based services during failure and failback to local services when the local server is restored.

### Automation in CI/CD

You can automate failover testing using **AWS CLI** to check if Route 53 health checks are correctly triggered.

```bash
# Simulate Local Failure
docker-compose down

# Check DNS Answer (Failover)
aws route53 test-dns-answer --hosted-zone-id <your-hosted-zone-id> --record-name <your-record-name> --record-type A
```

### Contributing

We welcome contributions! Please follow these steps to contribute:

1.  Fork the repository.
2.  Create a feature branch.
3.  Make your changes.
4.  Submit a pull request.

Ensure that your code is well-tested and documented.

## Diagram

Here is a diagram representing the architecture with local-to-cloud and cloud-to-local switching, **GitFlow** integration (Development → Staging → Production), **End-to-End (E2E) Testing**, **Route 53 DNS failover**, and the **shared database** between the **Admin Panel** and **Public Static Website**, and reflect the **shared database** and the **failover logic** for cloud-based services.:
```bash
+---------------------+      +---------------------+      +---------------------+
|                     |      |                     |      |                     |
|  Public Website     | ---> |  Admin Panel        | ---> |  AI Bot Services    |
|  (Next.js - Static) |      |  (Next.js - Dynamic)|      |  (Python, TensorFlow)|
|                     |      |                     |      |                     |
+---------------------+      +---------------------+      +---------------------+
        |                            |                             |
        v                            v                             v
 +----------------------+    +-----------------------+    +---------------------+
 |   Staging Database   |    |   Production Database |    |  Docker Containers  |
 |   (PostgreSQL)       |    |   (PostgreSQL)        |    |  (Web, Admin, AI)   |
 |   (Local)            |    |   (AWS RDS)           |    |  (Docker Compose)   |
 +----------------------+    +-----------------------+    +---------------------+
                         ^         |
                         |         |
                   +--------------------+
                   |   CI/CD Pipeline   |
                   | (GitHub Actions)   |
                   +--------------------+
                         |
                         v
            +----------------------------+
            |        Cloud Failover      |
            |    (AWS ECS/EC2, RDS)      |
            +----------------------------+
                         ^
                         |
                         v
               +---------------------+
               |    Route 53 DNS     |
               |   (Failover Logic)  |
               +---------------------+
                         ^
                         |
                         v
              +-----------------------+
              |    Local Development  |
              |     (Docker Compose) |
              |   (GitFlow - Dev)     |
              +-----------------------+
                         ^
                         |
                         v
              +--------------------------+
              |    End-to-End Testing    |
              | (Cypress, Playwright)    |
              +--------------------------+
                         ^
                         |
                         v
            +----------------------------+
            |    GitHub Actions CI/CD    |
            |    (Build, Test, Deploy)   |
            |   (GitFlow - Staging)     |
            +----------------------------+
                         ^
                         |
                         v
            +----------------------------+
            |     GitFlow - Production   |
            |  (Final Deployment)        |
            +----------------------------+

```

## Development Workflow with GitFlow

This project follows a structured GitFlow workflow to manage development and deployment across different environments. The process ensures that code is thoroughly tested and validated at each stage before reaching production.

1. **Local Development**:
   - Developers work on feature branches created from the `develop` branch.
   - Code changes are committed and pushed to the remote repository.
   - Local testing is performed using Docker to ensure consistency with the production environment.

2. **CI/CD Pipeline**:
   - Upon pushing changes, the CI/CD pipeline is triggered.
   - Automated tests are run to validate the code.
   - Docker images are built and stored in Amazon ECR.

3. **Staging Environment**:
   - Once the code passes initial tests, it is deployed to the staging environment.
   - Further testing is conducted to simulate real-world scenarios.
   - Feedback is gathered and any necessary changes are made.

4. **Production Deployment**:
   - After successful testing in staging, the code is merged into the `main` branch.
   - The CI/CD pipeline deploys the code to the production environment on AWS.
   - Continuous monitoring ensures the system operates smoothly.

This workflow allows for seamless integration and deployment, ensuring high-quality releases and minimizing downtime.