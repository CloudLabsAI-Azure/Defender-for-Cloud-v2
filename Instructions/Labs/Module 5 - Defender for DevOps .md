## Module 5 – Defender for DevOps 

## Task 1: Understanding CI/CD pipelines in Azure DevOps 

Continuous Integration and Continuous Deployment (CI/CD) are critical practices in modern software development, enabling teams to deliver code changes more frequently and reliably. Azure DevOps provides a robust platform to implement CI/CD pipelines. Here’s a basic understanding and a simple pipeline example to get you started.

### CI/CD Pipelines in Azure DevOps

**Continuous Integration (CI):**
CI involves automatically building and testing your code every time a team member commits changes to version control. The key goals are to detect errors quickly and to improve software quality.

**Continuous Deployment (CD):**
CD extends CI by automatically deploying all code changes to a production environment after the build and test stages are successful. This ensures that the software is always in a releasable state.

### Components of a CI/CD Pipeline

1. **Source Control:** Where the code resides (e.g., Git repository).
2. **Build:** The process of compiling the source code into executable artifacts.
3. **Test:** Automated tests run to validate the code.
4. **Release:** Deploying the artifacts to staging or production environments.

### Set up an Azure DevOps organization.

1. On your lab VM open **Edge Browser** on desktop and navigate to [Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137), and if prompted sign with the credentials.

    * Email/Username: <inject key="AzureAdUserEmail"></inject>

    * Password: <inject key="AzureAdUserPassword"></inject>

2. If prompted click **Ask later** on **Action Required** page.

3. On the next page accept defaults and click on continue.
   
   ![](images/AZ-400-odl.png)
   
4. On the **Almost Done...** page fill the captcha and click on continue. 

   ![](images/AZ-400-almost.png)
    
5. On the Azure DevOps page click on **Azure DevOps** located at top left corner and then click on **Organization Setting** at the left down corner

    ![Azure DevOps](images/az-400-lab3-(1).png)
    
6. In the **Organization Setting** window on the left menu click on **Billing (1)** .

    ![Azure DevOps](images/az-400-lab3-1.png)

1. Select **Setup Billing (2)** then click on **save (3)**.

    ![Azure DevOps](images/az-400-lab3-2.png)    

7. On the **MS Hosted CI/CD (1)** section under **Paid parallel jobs** enter value **1** and at the end of the page click on **Save (2)**.

    ![Azure DevOps](images/az-400-lab3-3.png)

1. In the **Organization Settings** window, click on **Settings** in the left menu. On the settings page, enable the **Creation of Classic Release Pipeline** option.

    ![](images/21.png)


1. On your lab computer, in a browser window open your Azure DevOps organization. Click on **New Project**. Give your project the name  **CICD (1)**, select visibility as **Private(2)**  and leave the other fields with defaults. Click on **Create project (3)**.

      ![](images/az400-m3-L4-03.png)
      
### **Create a Simple HTML File**

1. Click on **Repos (1)>Files (2)**, Select **Initialize (3)**. 

      ![](images/22.png)

1. First, let’s create a basic HTML file named `index.html`:

     ![](images/23.png)

1. Add the following contnet to `index.html` file:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>My Simple HTML Page</title>
    </head>
    <body>
        <h1>Hello, Azure DevOps!</h1>
        <p>This is a simple HTML page used to demonstrate CI/CD pipelines.</p>
    </body>
    </html>
    ```

1. Navigate back to the **Pipelines** pane in of the **Pipelines** hub.

1. In the **Create your first Pipeline** window, click **Create pipeline**.

    > **Note**: We will use the wizard to create a new YAML Pipeline definition based on our project.

1. On the **Where is your code?** pane, click **Azure Repos Git (YAML)** option.

   ![](images/10.png)

1. On the **Select a repository** pane, click **CICD**.

   ![](images/11.png)

1. On the **Configure your pipeline** pane, scroll down and select **Starter pipeline**.

   ![](images/12.png)

1. Define your build pipeline in a file named `azure-pipelines.yml` with the following content:

    ```yaml
    trigger:
    - main

    pool:
    vmImage: 'ubuntu-latest'

    steps:
    - task: UsePythonVersion@0
    inputs:
        versionSpec: '3.x'
    displayName: 'Set up Python'

    - script: |
        echo "Building the HTML project..."
        mkdir output
        cp index.html output/
    displayName: 'Build Project'

    - task: PublishBuildArtifacts@1
    inputs:
        pathToPublish: 'output'
        artifactName: 'html-artifact'
    displayName: 'Publish Artifacts'
    ```

1. This YAML file does the following:
- **Trigger**: Runs the pipeline when changes are pushed to the `main` branch.
- **Pool**: Uses an Ubuntu VM image for the build.
- **Steps**:
  - Set up Python (although not used in this case, you might need it for other tasks).
  - Create a directory called `output` and copy `index.html` to it.
  - Publish the contents of `output` as build artifacts.
   
8. Click **Save and Run** then click **Run** to start the Build Pipeline process.

   ![](images/14.png)

9. Wait for the Build Pipeline to complete successfully. Ignore any warnings regarding the source code itself, as they are not relevant for this lab exercise.

    > **Note**: Each task from the YAML file is available for review, including any warnings and errors.

### **Create a Release Pipeline**

1. Go to **Pipelines** > **Releases** > **New pipeline**.

    ![](images/24.png)


1. From the **Select a template** window, **choose** **Azure App Service Deployment** (Deploy your application to Azure App Service. Choose from Web App on Windows, Linux, containers, Function Apps, or WebJobs) under the **Featured** list of templates.    

1. Click **Apply**.

    ![](images/33.png)

1. Select the **CICD (1)**  in the Source (build pipeline) field. Click **Add (2)** to confirm the selection of the artifact.

    ![](images/25.png)

1. On the All pipelines > New Release Pipeline pane, ensure that the **stage 2** is selected. In the **Azure subscription(2)** dropdown list, Confirm the App Type is set to **Web App on Windows(3)**. Next, in the App Service name dropdown list, select the name of the **asclab-app (4)** web app.

   ![](images/28.png)

   >**Note**: After Selecting your Azure subscription and click Authorize. If prompted, authenticate by using the user account with the Owner role in the Azure subscription

1. Select the Task **Deploy Azure App Service**. In the **Package or Folder** field, update the default value of "$(System.DefaultWorkingDirectory)/**/*.zip" to **"$(System.DefaultWorkingDirectory)/_testing/html-artifact"**,

    ![](images/27.png)

1.  On the **All pipelines > New Release Pipeline** pane, click **Save** and, in the **Save** dialog box, click **OK**.

    ![](images/34.png)

1.  On the **All pipelines > New Release Pipeline** pane, click **Create release** .

    ![](images/29.png)

1. Wait until the release pipline successfully completed.

    ![](images/30.png)

1. Navigate to the Azure portal interface, navigate to the resource group **asclab**, in the list of resources, click the **asclab-app** web app.

1. On the web app blade, click **Browse**.

    ![](images/32.png)

1. Verify that the web page loads successfully in a new web browser tab.

    ![](images/31.png)


## Task 2: Identifying security issues in the pipeline 

To identify and address security issues in your CI/CD pipeline, you need to assess several aspects of your pipeline configuration and its deployment environment. Here are key areas to focus on and steps to help secure your Azure DevOps pipeline:

### 1. **Secrets and Credentials**

**Ensure Secrets are Secure:**
- **Use Azure DevOps Variable Groups** to manage sensitive information. Store secrets in the Azure DevOps Library and reference them using variable names rather than hardcoding them in your pipeline files.
- **Use Service Connections** for connecting to Azure or other services, which manage credentials securely.

**Example:**
Instead of hardcoding credentials:
```yaml
azureSubscription: 'my-azure-subscription'
```

Use a variable group:
```yaml
azureSubscription: $(azureSubscription)
```

### 2. **Access Control**

**Restrict Access:**
- **Limit access to your Azure DevOps project** to only those users who need it.
- **Use least privilege principle**: Ensure that only necessary permissions are granted to the service accounts and users.

**Configure Pipeline Permissions:**
- **Restrict who can edit or run pipelines** by setting appropriate permissions on the pipeline or repository level.

### 3. **Pipeline Code Review**

**Review Pipeline Configuration:**
- **Regularly review your pipeline YAML files** for any hardcoded secrets or insecure practices.
- **Implement pipeline as code practices** to ensure that changes are reviewed and approved through pull requests.

**Example YAML Review:**
Ensure that sensitive information is not exposed in the YAML file.

### 4. **Dependency Management**

**Review Dependencies:**
- **Ensure dependencies are up-to-date** and do not contain known vulnerabilities.
- **Use dependency scanning tools** to identify vulnerabilities in third-party packages.

**Example Tools:**
- **Azure Pipelines has built-in support** for tools like WhiteSource or Snyk to scan for vulnerabilities in dependencies.

### 5. **Secure Artifact Handling**

**Control Artifact Access:**
- **Ensure that build artifacts** are stored securely and access is controlled.
- **Use artifact repositories** with appropriate access controls.

**Example:**
When deploying artifacts, ensure that only authorized users can access or deploy them.

### 6. **Pipeline and Deployment Security**

**Validate Deployment Configurations:**
- **Ensure that deployment tasks are configured securely**, such as using service connections or managed identities instead of hardcoded credentials.

**Monitor and Audit:**
- **Enable auditing and monitoring** for your pipeline activities and deployments.
- **Regularly review audit logs** to track access and changes.

### 7. **Pipeline Security Tools**

**Integrate Security Scanning:**
- **Integrate security scanning tools** into your pipeline to detect vulnerabilities in your code, configuration, and dependencies.

**Example Tasks:**
- **Static Analysis**: Run static code analysis tools to identify security issues in code.
- **Dynamic Analysis**: Implement dynamic application security testing (DAST) tools to test running applications for vulnerabilities.

### 8. **Example Pipeline Security Configuration**

Here’s a more secure version of the YAML pipeline, incorporating best practices:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  azureSubscription: $(azureSubscription) # Securely managed variable

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.x'
  displayName: 'Set up Python'

- script: |
    echo "Building the HTML project..."
    mkdir output
    cp index.html output/
    cd output
    zip -r html-artifact.zip *
  displayName: 'Build and Zip Project'

- task: PublishBuildArtifacts@1
  inputs:
    pathToPublish: 'output/html-artifact.zip'
    artifactName: 'html-artifact'
  displayName: 'Publish Artifacts'

- task: AzureRmWebAppDeployment@4
  inputs:
    azureSubscription: $(azureSubscription) # Securely managed variable
    appName: '<your-web-app-name>'
    package: '$(System.DefaultWorkingDirectory)/_your-build-pipeline/html-artifact/html-artifact.zip'
  displayName: 'Deploy to Azure Web App'
```

### 9. **Documentation and Training**

**Educate Your Team:**
- **Provide training on security best practices** for CI/CD pipelines.
- **Keep documentation updated** on secure pipeline practices and configuration guidelines.

By focusing on these aspects, you can significantly improve the security of your Azure DevOps pipelines and protect your applications and data.
## Exercise 3: Overview of GitHub Advanced Security (GHAS) [Read-Only] 

### Overview of GitHub Advanced Security (GHAS)

GitHub Advanced Security (GHAS) is a suite of security tools built into the GitHub platform designed to help developers secure their code and workflows. It includes features such as code scanning, secret scanning, and dependency review to identify and remediate security vulnerabilities and exposures.

### Step-by-Step Guide to GitHub Advanced Security

#### 1. **Enable GitHub Advanced Security**
To use GHAS, you need to have GitHub Advanced Security enabled for your repository. This typically requires a GitHub Enterprise subscription.

- Navigate to your repository on GitHub.
- Click on `Settings`.
- In the `Security` section, find `GitHub Advanced Security` and enable it.

#### 2. **Configure Code Scanning**

**Code scanning** helps detect vulnerabilities and errors in your code by running static analysis tools.

- Go to the `Security` tab of your repository.
- Click on `Set up code scanning`.
- You can choose from different options like `CodeQL Analysis`, which is a powerful tool provided by GitHub. Select the `Set up this workflow` button for `CodeQL Analysis`.
- Review the configuration file (e.g., `.github/workflows/codeql-analysis.yml`). Modify it if needed and commit it to your repository.

#### 3. **Run Code Scanning**

- Once configured, code scanning runs automatically on the specified events (like pushes and pull requests).
- You can also manually trigger a scan by going to the `Actions` tab, finding the `CodeQL` workflow, and clicking `Run workflow`.

#### 4. **Review Code Scanning Results**

- Navigate to the `Security` tab, and under `Code scanning alerts`, you'll see a list of detected issues.
- Click on any alert to get detailed information about the vulnerability and recommended fixes.

#### 5. **Configure Secret Scanning**

**Secret scanning** detects secrets (like API keys and tokens) that may have been accidentally committed to your repository.

- Go to the `Security` tab of your repository.
- Click on `Set up secret scanning`.
- GitHub automatically scans for patterns that match common secret types and alerts you if any are found.

#### 6. **Review Secret Scanning Results**

- Navigate to the `Security` tab, and under `Secret scanning alerts`, you'll see a list of detected secrets.
- Click on any alert to view details and follow the steps to revoke or rotate the compromised secrets.

#### 7. **Set Up Dependency Review**

**Dependency review** helps you understand and remediate vulnerable dependencies in your project.

- Ensure your project has a dependency manifest file (e.g., `package.json`, `pom.xml`).
- GitHub automatically generates dependency graphs and checks for known vulnerabilities in your dependencies.

#### 8. **Review Dependency Alerts**

- Navigate to the `Security` tab, and under `Dependency review`, you'll see alerts for vulnerable dependencies.
- Click on any alert to see details about the vulnerability and recommended versions to update to.

#### 9. **Manage Security Policies**

- In the `Security` tab, you can also manage security policies by setting up a `security.md` file to inform users about your project's security practices and how they can report vulnerabilities.

#### 10. **Continuous Monitoring and Alerts**

- GitHub Advanced Security continuously monitors your repository and generates alerts for any new issues found.
- Make it a habit to regularly review the `Security` tab and address any new alerts promptly.

## Exercise 4: Overview of Defender for DevOps (including pricing) [Read-Only] 

#Defender for DevOps is a security solution by Microsoft designed to enhance the security of DevOps environments. It provides a range of tools and features to help secure the software development lifecycle (SDLC) and protect against threats that target DevOps processes. Here's an overview:

### **Key Features:**

1. **Security for CI/CD Pipelines:**
   - **Code Scanning:** Identifies vulnerabilities and security issues in your code before deployment.
   - **Dependency Scanning:** Detects known vulnerabilities in dependencies and libraries used in your projects.

2. **Integration with Azure DevOps and GitHub:**
   - **Azure DevOps:** Integrates with Azure Pipelines, Boards, Repos, and other Azure DevOps services to secure the DevOps lifecycle.
   - **GitHub:** Provides security insights and recommendations directly in your GitHub repositories.

3. **Container Security:**
   - **Image Scanning:** Scans container images for vulnerabilities before they are deployed.
   - **Runtime Protection:** Monitors running containers for security issues and potential threats.

4. **Infrastructure as Code (IaC) Security:**
   - **IaC Scanning:** Analyzes your IaC templates for security misconfigurations and vulnerabilities.

5. **Policy Management:**
   - **Compliance Policies:** Enforces security policies and compliance requirements across your DevOps processes.

6. **Threat Detection and Response:**
   - **Alerts and Notifications:** Provides real-time alerts on detected security threats and vulnerabilities.
   - **Incident Management:** Helps manage and respond to security incidents effectively.

### **Pricing:**

Defender for DevOps is included in Microsoft Defender for Cloud, and its pricing is generally based on the overall Defender for Cloud plan. Pricing may vary based on:

1. **Number of Resources:**
   - The cost may be influenced by the number of resources (e.g., pipelines, repositories, containers) being protected.

2. **Features and Plans:**
   - Microsoft Defender for Cloud offers different pricing tiers (e.g., Free, Standard, and Enhanced) that include various features and levels of protection. Defender for DevOps features are generally available in the Standard or Enhanced tiers.

3. **Azure Consumption:**
   - Costs are also related to the overall consumption of Azure resources and services.

For the most accurate and up-to-date pricing, it's best to consult the [Microsoft Defender for Cloud pricing page](https://azure.microsoft.com/en-us/pricing/details/defender-for-cloud/) or contact Microsoft sales support directly.

## Exercise 5: Securing your pipeline with GHAS and Defender for DevOps  

To secure your pipeline with GitHub Advanced Security (GHAS) and Microsoft Defender for DevOps, you can integrate these tools to enhance your pipeline's security posture. Here’s a example of how to use GHAS and Defender for DevOps for security:

### 1. **GitHub Advanced Security (GHAS)**

**GitHub Advanced Security** provides several features to secure your code, including code scanning, secret scanning, and dependency review.

#### **Enable Code Scanning**

1. **Go to Your GitHub Repository:**
   - Navigate to the repository you want to secure.

2. **Set Up Code Scanning:**
   - Go to **Security** > **Code scanning alerts**.
   - Click **Set up code scanning**.
   - Choose a code scanning tool. GitHub offers built-in support for CodeQL, or you can use third-party tools.

3. **Add a CodeQL Workflow:**
   - Add a `.github/workflows/codeql-analysis.yml` file to your repository to configure the CodeQL analysis:

   ```yaml
   name: "CodeQL"

   on:
     push:
       branches: [main]
     pull_request:
       branches: [main]

   jobs:
     analyze:
       name: Analyze
       runs-on: ubuntu-latest
       steps:
         - name: Checkout code
           uses: actions/checkout@v3

         - name: Set up CodeQL
           uses: github/codeql-action/setup@v2
           with:
             languages: 'python'

         - name: Autobuild
           uses: github/codeql-action/autobuild@v2

         - name: Perform CodeQL Analysis
           uses: github/codeql-action/analyze@v2
   ```

#### **Enable Secret Scanning**

1. **Navigate to Your Repository Settings:**
   - Go to **Security** > **Secret scanning**.
   - Ensure that secret scanning is enabled to detect any accidentally committed secrets.

2. **Configure Secret Scanning Alerts:**
   

#### **Set Up Dependency Review**

1. **Configure Dependency Review:**
   - Go to **Security** > **Dependabot alerts**.
   - Enable **Dependabot** for automated dependency updates and reviews.

### 2. **Microsoft Defender for DevOps**

**Microsoft Defender for DevOps** provides security features for Azure DevOps, including security posture management and vulnerability assessments.

#### **Integrate with Azure DevOps**

1. **Navigate to Azure DevOps Portal:**
   - Go to your Azure DevOps organization.

2. **Enable Microsoft Defender for Cloud:**
   - Go to **Organization Settings** > **Security** > **Microsoft Defender for Cloud**.
   - Enable Microsoft Defender for Cloud for your Azure DevOps organization.

#### **Configure Policies and Alerts**

1. **Set Up Policies:**
   - Configure security policies and rules in Defender for Cloud to monitor and protect your Azure DevOps resources.
   - Ensure that policies are set to alert on security misconfigurations and vulnerabilities.

2. **Review Security Recommendations:**
   - Periodically review security recommendations provided by Defender for Cloud.
   - Implement suggested actions to address vulnerabilities and security risks.

#### **Monitor and Respond**

1. **Monitor Security Alerts:**
   - Regularly check the **Microsoft Defender for Cloud** dashboard for security alerts and incidents.

2. **Respond to Issues:**
   - Investigate and resolve any security issues or vulnerabilities detected by Microsoft Defender for Cloud.

### **Simple Example Pipeline Integration**

Here’s how you might configure a pipeline in GitHub Actions to integrate with GHAS and Microsoft Defender for DevOps:

**GitHub Actions Pipeline with Code Scanning:**

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: |
          pytest

      - name: CodeQL Analysis
        uses: github/codeql-action/analyze@v2
        with:
          languages: 'python'

      - name: Publish build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: build/
```

In this task:
- **Code Scanning** is enabled with CodeQL to identify security vulnerabilities in your code.
- **Secret Scanning** and **Dependency Review** are managed through GitHub’s settings.
- **Microsoft Defender for Cloud** integrates with Azure DevOps to monitor and secure the environment.

By integrating GHAS and Defender for DevOps, you ensure that your pipeline is better protected against security threats and vulnerabilities.

## Exercise 6: Connecting your Azure DevOps environment to MDC 

1. Search and select **Microsoft Defender for Cloud** from the portal

    ![alert_detected](images/mls2.png)

1. Select **skip** on **Getting started** tab.

    ![alert_detected](images/mls1.png)

1. Select **Environment settings** under Management > **+Add environment** > **Azure DevOps**

    ![alert_detected](images/advlab51.png)

1. On the **Azure DevOps Connection** page, under **Account details**, provide the below settings.

   | Setting  | Value |
   -----------|---------
   | Connector name | AzureDevopsconnector |
   | Subscription | Choose the default subscription |
   | Resource group | Lab-VM |
   | Location | Select any supporting region |

    ![alert_detected](images/advlab52.png)

1. Select **Next: Configure access**

1. Select **Authorize**. Ensure you're authorizing the correct Azure Tenant using the drop-down menu in Azure DevOps and by verifying you're in the correct Azure Tenant in Defender for Cloud.

1. In the popup dialog, read the list of permission requests, and then select **Accept**.

    ![alert_detected](images/advlab53.png)

1. Leave all other settings as default.

1. Select **Next: Review and generate**.

1. Review the information, and then select **Create**.

1. Wait for some time to view the connector on the **Environment settings** page.

1. Navigate to **DevOps Security** under **Cloud Security**.

    ![alert_detected](images/advlab55.png)

1. The **DevOps security findings** and **DevOps security results** are listed on the page, which helps to review the DevOps security posture.

    ![alert_detected](images/m51.png)

   >**Note:** It might take upto 8hrs to reflect the real-time status.

1. Navigate to **DevOps workbook** and change the toggle to **Yes**, which provides an overview of the tabs provided below

    ![alert_detected](images/m55.png)

    ![alert_detected](images/m52.png)

1. Navigate to the **Code** tab and scroll down, click on the **Severity** section to open the individual findings, and click on **Information** which in turn provides detailed findings and the issue location.

    ![alert_detected](images/m53.png)

1. Similarly, navigate to the **OSS Vulnerabilities** tab and identify the issues, then take note of the recommendations provided to resolve the issues.

    ![alert_detected](images/m54.png)

## Exercise 7: Integrating non-MS security scan solutions with MDC 

Integrating non-Microsoft security scanning solutions with Microsoft Defender for Cloud (MDC) involves setting up the external solution, configuring it to send its findings to MDC, and then validating the integration. Here's a simple example using an open-source security scanning tool, Trivy, for container image scanning:

### Prerequisites

1. **Azure Subscription**: Ensure you have an active Azure subscription with MDC enabled.
2. **Trivy**: Installed and configured on your local machine or CI/CD pipeline.
3. **Azure CLI**: Installed and configured on your local machine.

### Steps

#### 1. Setup Trivy

Install Trivy if it's not already installed:

```sh
brew install aquasecurity/trivy/trivy
```

#### 2. Scan a Docker Image

Use Trivy to scan a Docker image. For this example, let's scan the `nginx` image:

```sh
trivy image nginx
```

This command will output the vulnerabilities found in the `nginx` image.

#### 3. Format the Scan Results

Trivy can output results in various formats such as JSON. For integration with MDC, JSON format will be used:

```sh
trivy image -f json -o results.json nginx
```

#### 4. Create a Custom Logic App for Integration

1. **Create a Logic App**: In the Azure Portal, create a new Logic App.
2. **Add a Trigger**: Set up a trigger for the Logic App. This can be an HTTP request received from your CI/CD pipeline or a scheduled trigger.
3. **Add an Action**: Configure the Logic App to send the formatted Trivy results to MDC.

Here’s a sample Logic App workflow:

- **Trigger**: When an HTTP request is received.
- **Action**: HTTP - Send a request to MDC’s API with the Trivy results.

```json
{
    "type": "HTTP",
    "method": "POST",
    "uri": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Security/securitySolutions/{securitySolutionName}?api-version=2020-01-01",
    "headers": {
        "Content-Type": "application/json",
        "Authorization": "Bearer {accessToken}"
    },
    "body": {
        "findings": "@{triggerOutputs().body}"
    }
}
```

#### 5. Trigger the Logic App

From your CI/CD pipeline or manually, trigger the Logic App, sending the `results.json` generated by Trivy.

#### 6. Validate the Integration

Check the MDC dashboard to ensure the findings from Trivy are reflected.

### Example CI/CD Pipeline Integration

Here's a simple example using GitHub Actions:

```yaml
name: Security Scan

on: [push]

jobs:
  trivy-scan:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Install Trivy
      run: |
        sudo apt-get update
        sudo apt-get install -y trivy
    - name: Scan Docker Image
      run: |
        trivy image -f json -o results.json nginx
    - name: Trigger Logic App
      run: |
        curl -X POST \
          -H "Content-Type: application/json" \
          -H "Authorization: Bearer ${{ secrets.AZURE_ACCESS_TOKEN }}" \
          --data @results.json \
          https://management.azure.com/subscriptions/${{ secrets.AZURE_SUBSCRIPTION_ID }}/resourceGroups/${{ secrets.AZURE_RESOURCE_GROUP }}/providers/Microsoft.Security/securitySolutions/${{ secrets.AZURE_SECURITY_SOLUTION_NAME }}?api-version=2020-01-01
```

### Conclusion

This is a basic example demonstrating how to integrate Trivy, a non-Microsoft security scanning tool, with Microsoft Defender for Cloud. By customizing the Logic App and CI/CD pipeline configurations, you can adapt this example to integrate other security tools with MDC.

## Task 8: Role of Defender Cloud Security Posture Management (DCSPM) 

### Task 1: Understanding Microsoft Data Security Posture Management

**Data Security Posture Management (DSPM)** allows security teams to get ahead of their data risks and prioritize security issues that could result in a data breach. With DSPM you can:
     
   - Automatically discover sensitive data resources across multiple clouds.

   - Evaluate data sensitivity, data exposure, and how data flows across the organization.

   - Proactively and continuously uncover risks that might lead to data breaches.

   - Detect suspicious activities that might indicate ongoing threats to sensitive data resources.
     
1. Defender for Cloud leverages **DSPM** data to prioritize critical data risks by distinguishing them from other risks by:

     - Highlighting attack paths of internet-exposed VMs that have access to sensitive data stores.
     - Allowing you to leverage Cloud Security Explorer to identify misconfigured data resources that are publicly accessible and contain sensitive data, across multi-cloud environments. 

      ![](images/def1.png)

2. Data sensitivity context is also used in Security Alerts and you can quickly filter based on the type of Sensitivity Information. Navigate to **Security alerts** click on **Add filters**, and set it to **Sensitivity info types**.

     ![](images/def3.png) 

### Task 2: Enabling Defender CSPM plan

In this exercise, you will learn how to enable Defender for CSPM and leverage Defender for CSPM Capabilities

   >**Note:** To gain access to the capabilities provided by Defender CSPM, you'll need to <a href="https://learn.microsoft.com/en-us/azure/defender-for-cloud/enable-enhanced-security">enable the Defender Cloud Security Posture Management (CSPM) plan </a> on your subscription

1. **Defender Cloud Security Posture Management**, will reduce the critical risks by:

     - **Monitor your multi-cloud security posture**: It gets continuous security assessments of your resources running across Microsoft Azure, AWS, Google Cloud Platform, and on-premises.
     
     - **Prioritize risks with contextual insights**: Identifies your most critical risks with insights from the security operations center (SOC), DevOps, APIs, Microsoft Defender External Attack Surface Management, Microsoft Entra Permissions Management, and Microsoft Purview, all in a single view.
     
     - **Get agent and agentless vulnerability scanning**: It gets continuous, real-time monitoring with agentless vulnerability scanning and gains deeper protection from built-in agents.
     
     - **Maintain compliance with multi-cloud benchmarks**: It follows best practices for multi-cloud security compliance with controls mapped to major regulatory industry benchmarks, such as the Center for Internet Security, the Payment Card Industry, and the National Institute for Standards and Technology, in a central dashboard. 

1. In **Azure Portal**, search for **Microsoft Defender for Cloud (1)** and then click on it from the search results **(2)**. 

      ![](images/m1-img1.png)

2. From **Defender for Cloud** menu, click on **Environment Settings (1)** page and select your subscription **(2)**.

      ![](images/m1-img2.png)

3. In the **Defender plans** page, select **Defender CSPM** turn the status to **On (1)** and select **Settings & monitoring (2)**.

      ![](images/m1-img3.png)

4. Turn **On (1)** the **Agentless scanning for machines (preview)** and click **Continue (2)**.

      ![](images/m1-img4.png)

5. Click on **Save** to save the changes. 

   >**Note:** Agentless scanning for VMs provides vulnerability assessment and software inventory in 24 hours. Leave the setup and comeback after 24 hours.

      ![](images/m1-img5.png)


 