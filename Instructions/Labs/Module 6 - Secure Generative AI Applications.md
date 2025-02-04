# Lab 6: Secure Generative AI Applications (Read-Only)

## Overview:

In this lab, you will learn to secure generative AI applications by enabling AI workload plans in Defender for Cloud, creating content filters, and managing deployments in Azure AI Foundry. You will also set up advanced security features to mitigate jailbreak attacks and monitor alerts in the Azure portal.

## Architecture:

The architecture showcases a secure DevOps and AI-driven cloud solution, integrating Azure and security services. The workflow begins with a developer pushing code to Azure DevOps, which then stores container images in Azure Container Registry (ACR). These images are deployed to AKS (Azure Kubernetes Service) or EKS (Amazon Elastic Kubernetes Service), while Key Vault manages secrets and credentials.  
Security is enforced through Defender for Cloud, API Gateway (WAF), and Entra ID for authentication. The deployed applications interact with Azure OpenAI for AI processing, while data is stored in Azure SQL and Storage Accounts. Logs are collected in Log Storage and monitored via Microsoft Sentinel, ensuring security and compliance across the infrastructure.

## Architecture Diagram

![](images/arch.png)

## Estimated Duration: 50 min

## Lab Objectives : 

In this lab, you will complete the following tasks:

- Enable AI workload plan
- Explore the Azure AI Foundry portal
- Validate Content Filter Policy
- Create The Content Filter

### Task 1: Enable AI workload plan

1. In the search bar, type **Defender for Cloud* (1)** and select **Microsoft Defender for Cloud (2)** from the results.

   ![](images/178.png)

1. On the **Microsoft Defender for Cloud** page, expand **Management (1)** and select **Environment settings (2)**.

   ![](./images/175.png)
   
1. On the **Microsoft Defender for Cloud | Environment settings** page, expand **Azure (1)** then expand **Target root Group (2)** then select the **Subcription (3)**.

   ![](./images/174.png)

1. Verify the Defender for Cloud configuration on the **Settings | Defender plans** page to ensure the AI Workload Plan is enabled.

   ![](./images/176.png)

1. Verify that the models have an active Content Filter Policy in place.

### Task 2: Explore Azure AI Foundry portal

1. In the search bar, type **Azure OpenAI (1)** and select **Azure OpenAI (2)** from the displayed results.  

   ![](./images/177.png)  

2. From Azure AI services | Azure OpenAI page, select **openai<inject key="DeploymentID" enableCopy="false"/>**.

   ![](./images/179.png)

1. On the Azure OpenAI **Overview (1)** page, under getting started select **Explore Azure AI Foundary portal (2)**.

   ![](./images/180.png)

1. Right-click on the [GitHub repository](https://github.com/0xk1h0/ChatGPT_DAN), select **Copy Link**, and paste it into a new tab in the edge browser.

1. Under **ChatGPT "DAN" (and other "Jailbreaks") PROMPTS**, expand **The DAN 6.0 Prompt (1)** and copy the content located under **DAN 6.0 Prompt (2)**.

   ![](./images/181.png)

1. Navigate back to the **Azure AI Foundry portal**. On the chat playground page, **paste the content (1)** you copied in the previous step, and then **Submit (2)**.

   ![](./images/182.png)

### Task 3: Validate Content Filter Policy

1. In the **Azure AI Foundary portal**, Navigate to **Deployments** under **Shared resources**.

1. Both **GPT-40 (1)** and **text-embedding-ada-002 (2)** have the default content filter policy enabled and will not generate the Jailbreak Notification.

   ![](./images/183.png)

### Task 4: Create The Content Filter

1. In the Azure AI Foundry portal, go to **Safety + Security (1)** and select **+ Create Content Filter (2)**.

   ![](./images/185.png)

1. Create the Content Filter with the following details:

    - **Basic Information:**

      - Name: **CustomContentFilterWithJailBreakProtection (1)**
      - Click on **Next (2)**

        ![](./images/186.png)

    - **Input Filter:**

      - Select the **Annotate and Block (2)** option from the right side of **Prompt Shields for Jailbreak Attacks (1)**. Then, in the drop-down menu, choose **Off (2)** and click **Next (4)**.
 
        ![](./images/187.png)
  
    - **Output Filter:**

      - Leave as default and click on **Next**.

        ![](./images/188.png)

    - **Deployment (optional):**

      - Select all three deployments **(1)**, then click **Next (2)**.

       ![](./images/189.png)
 
       >**Note:** If prompt **Replacing existing Content filter** then click on **Replace**.

         ![](./images/190.png)

    - **Review:**

      - On the **Review** page, click **Create Filter**.

        ![](./images/191.png)

1. Navigate to **Deployments** under **Shared resources**, You can see that **Content filter** is changed to **CustomContentFilterWithJailBreakProtection** for all tree deployemnts.

   ![](./images/192.png)

1. Go to **Playground** and select **Chat (1)** in the Azure AI Foundry portal. Enter **What can you do? (2)** in the input field, then review the generated response **(3)**.

   ![](./images/193.png)

1. Right-click on the [GitHub repository](https://github.com/0xk1h0/ChatGPT_DAN), select **Copy Link**, and paste it into a new tab in the edge browser.

1. Under **ChatGPT "DAN" (and other "Jailbreaks") PROMPTS**, expand **The DAN 6.0 Prompt (1)** and copy the content located under **DAN 6.0 Prompt (2)**.

   ![](./images/181.png)

1. Navigate back to the **Azure AI Foundry portal**. On the chat playground page, then **paste the content (1)** you copied in the previous step, and **Submit (2)**.

   ![](./images/182.png)

1. Notice the response:

   ![](./images/194.png)

1. Navigate back to azure portal, Search for **Defender for cloud (1)** and select **Microsoft Defender for Cloud (2)**.

   ![](images/178.png)

1. Naviagte to **Security alerts** under **General**.

   ![](images/195.png)

1. Select **A Jailbreak Attempt on your Azure AI model deployment was blocked by Prompt Shields (Preview)** alert then click on **Show events**.

   ![](images/196.png)

1. Take note of the suspicious segment in the Application and Prompt.

   ![](images/197.png)

## Summary:

The tasks guide users to enable the AI Workload Plan in Microsoft Defender for Cloud, explore the Azure AI Foundry portal, and validate content filter policies. Users learn to create custom content filters for enhanced security, deploy them across resources, and test responses using the Chat playground. Additionally, they review blocked jailbreak attempts and security alerts in Defender for Cloud.


## You have successfully completed the lab
