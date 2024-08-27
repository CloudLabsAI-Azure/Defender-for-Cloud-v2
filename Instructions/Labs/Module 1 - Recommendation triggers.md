# **Lab 1 : Response Triggers (Read-Only)**

## Task 1: Initiating the Flow

You can respond to recommendations, alerts, and compliance findings using **Logic Apps** (we'll create one in Module 2). The response logic app can be triggered either automatically or manually.

### Setting Up Automated Responses:

1. **Workflow Automation:** Configure it to automatically trigger the logic app when Defender for Cloud detects a finding. You can find the setup process [here](https://learn.microsoft.com/en-us/azure/defender-for-cloud/workflow-automation).
2. **Azure Policies:** These can be used to configure automation at [scale](https://learn.microsoft.com/en-us/azure/defender-for-cloud/workflow-automation#configure-workflow-automation-at-scale). However, this workshop does not cover this setup.

## Task 2: Scenarios for Automated Triggers

Automated triggers are particularly useful in the following situations:

- You have strict policy guidelines and aim to keep risk within acceptable levels **without any exceptions**.
- You've already evaluated the trigger, confirmed that no risk exceptions exist, and want to initiate remediation as soon as the risk is detected.
- The risk exceeds your tolerance levels.

## Task 3: Scenarios for Manual Triggers

Manual triggers are best suited for the following scenarios:

- You're in the early stages of deployment.
- You plan to remediate only a select few unhealthy resources and set exceptions for the rest.
- Your change management system isn't directly integrated with Logic Apps for automatic deployments.

![Workshop Focus](./images/recommendation-manual-trigger.png "Focus areas for the workshop and manual trigger")

Next, let's move on to [creating the response logic app](./Module%202%20-%20Writing%20Logic%20App.md).