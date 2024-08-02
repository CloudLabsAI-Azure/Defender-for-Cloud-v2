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


## Exercise 2: Identifying security issues in the pipeline 

#### Task 1: Activate Mend Bolt extension

In this task, you will activate WhiteSource Bolt in the newly generated Azure Devops project.

1.  On your lab computer, in the web browser window displaying the Azure DevOps portal with the **CICD** project open, click on the marketplace icon > **Browse Marketplace**.

    ![Browse Marketplace](images/browse-marketplace.png)

1.  On the MarketPlace, search for **Mend Bolt (formerly WhiteSource)** and open it. Mend Bolt is the free version of the previously known WhiteSource tool, which scans all your projects and detects open source components, their license and known vulnerabilities.

    > Warning: make sure you select the Mend **Bolt** option (the **free** one)!

1.  On the **Mend Bolt (formerly WhiteSource)** page, click on **Get it for free**.

    ![Get Mend Bolt](images/mend-bolt.png)

1.  On the next page, select the desired Azure DevOps organization and **Install**. **Proceed to organization** once installed.

1.  In your Azure DevOps navigate to **Organization Settings** and select **Mend** under **Extensions**. Provide your Work Email (**your lab personal account**, e.g. using AZ400learner@outlook.com instead of student@microsoft.com ), Company Name and other details and click **Create Account** button to start using the Free version.

    ![Get Mend Account](images/20.png)

#### Task 2: Create and Trigger a build

In this task, you will create and trigger a CI build pipeline within  Azure DevOps project. You will use **Mend Bolt** extension to identify vulnerable OSS components present in this code.

1.  On your lab computer, from the **CICD** Azure DevOps project, in the vertical menu bar on the left side, navigate to the **Pipelines>Pipelines** section, click  **New Pipeline**.

1.  On the **Where is your code?** window, select **Azure Repos Git (YAML)** and select the **CICD** repository.

    ![Select Pipeline](images/10.png)

1.  On the **Configure** section, choose **Existing Azure Pipelines YAML file (1)**. Provide the following **path (2)** **/.ado/eshoponweb-ci-mend.yml** and click **Continue (3)**.

    ![Select Pipeline](images/19.png)

1.  Review the pipeline and click on **Run**. It will take a few minutes to run successfully.
    > **Note**: The build may take a few minutes to complete. The build definition consists of the following tasks:
    - **DotnetCLI** task for restoring, building, testing and publishing the dotnet project.
    - **Whitesource** task (still keeps the old name), to run the Mend tool analysis of OSS libraries.
    - **Publish Artifacts** the agents running this pipeline will upload the published web project.

1.  While the pipeline is executing, lets **rename** it to identify it easier (as the project may be used for multiple labs). Go to **Pipelines/Pipelines** section in Azure DevOps project, click on the executing Pipeline name (it will get a default name), and look for **Rename/move** option on the ellipsis icon. Rename it to **cicd-mend** and click **Save**.

    ![Rename Pipeline](images/18.png)

1.  Once the pipeline execution has finished, you can review the results. Open the latest execution for  **eshoponweb-ci-mend** pipeline. The summary tab will show the logs of the execution, together with related details such as the repository version(commit) used, trigger type, published artifacts, test coverage, etc.

1. On the **Mend Bolt** tab, you can review the OSS security analysis. It will show you details around the inventory used, vulnerabilities found (and how to solve them), and an interesting report around library related Licenses. Take some time to review the report.

    ![Mend Results](images/mend-results.png)

## Exercise 3: Overview of GitHub Advanced Security (GHAS) [Read-Only] 

### Overview of GitHub Advanced Security (GHAS)

GitHub Advanced Security (GHAS) is a suite of security tools built into the GitHub platform designed to help developers secure their code and workflows. It includes features such as code scanning, secret scanning, and dependency review to identify and remediate security vulnerabilities and exposures.

### Step-by-Step Guide to GitHub Advanced Security

#### 1. **Enable GitHub Advanced Security**
To use GHAS, you need to have GitHub Advanced Security enabled for your repository. This typically requires a GitHub Enterprise subscription.

- Navigate to your repository on GitHub.
- Click on `Settings`.
- In the `Security` section, find `GitHub Advanced Security` and enable it.

https://github.com/ghas-bootcamp/ghas-bootcamp fork this repo 

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

### Overview of Microsoft Defender for DevOps

Microsoft Defender for DevOps is a comprehensive security solution designed to protect your DevOps environments, including CI/CD pipelines, code repositories, and infrastructure as code (IaC) configurations. It helps identify and remediate vulnerabilities, enforce security policies, and secure the entire DevOps lifecycle.


#### 1. **Integrate with DevOps Tools**

Integrate Defender for DevOps with your CI/CD pipelines and code repositories to start scanning for vulnerabilities.

**Azure DevOps:**

- In the Azure DevOps portal, go to `Project Settings`.
- Under `Pipelines`, select `Service connections`.
- Click on `New service connection` and choose `Azure Resource Manager`.
- Follow the prompts to authorize and configure the service connection.

**GitHub:**

- In the GitHub repository, navigate to `Settings`.
- Under `Security`, select `Integrations`.
- Click on `Configure` next to Microsoft Defender for DevOps and follow the instructions to complete the integration.

#### 3. **Configure Security Policies**

Set up security policies to define the security requirements and best practices for your DevOps environment.

- In the Azure portal, go to `Microsoft Defender for Cloud`.
- Select `Security policy`.
- Choose the subscription or resource group where you want to apply the policy.
- Configure the policy settings according to your security requirements.

#### 4. **Enable Continuous Assessment**

Enable continuous assessment to automatically scan your DevOps pipelines and code repositories for security vulnerabilities.

- In the Azure portal, go to `Microsoft Defender for Cloud`.
- Under `Defender plans`, enable continuous assessment for your DevOps environment.
- Configure the settings to define the scope and frequency of the assessments.

#### 5. **Monitor and Review Security Alerts**

Monitor the security alerts generated by Defender for DevOps to identify and remediate vulnerabilities.

- Navigate to `Microsoft Defender for Cloud` in the Azure portal.
- Go to `Security alerts` to view the list of detected security issues.
- Click on any alert to get detailed information about the vulnerability and recommended remediation steps.

#### 6. **Remediate Vulnerabilities**

Follow the remediation recommendations provided by Defender for DevOps to fix the detected vulnerabilities.

- Review the alert details to understand the nature of the vulnerability.
- Implement the suggested fixes in your code, configuration files, or pipeline settings.
- Rescan the environment to ensure the vulnerabilities have been addressed.

#### 7. **Implement Best Practices**

Adopt security best practices to minimize the risk of vulnerabilities in your DevOps environment.

- Use secure coding practices and code review processes to identify potential security issues early.
- Regularly update dependencies and use vulnerability management tools to keep your environment secure.
- Implement access controls and least privilege principles to protect sensitive data and resources.
- Use Infrastructure as Code (IaC) security tools to scan and validate your IaC templates.

### Pricing

The pricing for Microsoft Defender for DevOps is typically based on the number of resources being monitored and the volume of data processed. Here is an overview of the pricing components:

- **Base Pricing:** There might be a base fee associated with enabling Defender for DevOps, depending on the Azure subscription plan.
- **Resource Monitoring:** Charges are based on the number of resources (e.g., virtual machines, containers) being monitored.
- **Data Ingestion:** Costs for the volume of data processed and analyzed by Defender for DevOps.
- **Alerting and Reporting:** Some plans may include charges for advanced alerting and reporting features.

To get detailed and up-to-date pricing information, you can refer to the [Microsoft Defender for DevOps pricing page](https://azure.microsoft.com/en-us/pricing/details/defender-for-cloud/) on the Azure website.

By following these steps, you can effectively utilize Microsoft Defender for DevOps to secure your DevOps environment and ensure the security of your CI/CD pipelines and code repositories.

## Exercise 5: Securing your pipeline with GHAS and Defender for DevOps  

### Task 1: Sign up and configure the eShopOnWeb team project in Azure DevOps

1. Open the **Edge browser**, and navigate to **Azure DevOps** using the link below. Select **Start Free**, and sign in with the credentials provided in the Environment variables.

   ```
    https://dev.azure.com
   ```

      ![setup](images/lab1-image1.png)

1. Navigate to **azuredevopsdemogenerator** using the link below. This utility site will automate the process of creating a new Azure DevOps project within your account that is prepopulated with content (work items, repos, etc.) required for the lab. For more information on the site, please see [https://docs.microsoft.com/en-us/azure/devops/demo-gen](https://docs.microsoft.com/en-us/azure/devops/demo-gen).

   ```
   https://azuredevopsdemogenerator.azurewebsites.net/
   ```
  
1. Click on **Sign in** and log in using the Microsoft account associated with your Azure DevOps subscription.

    ![](images/lab1-image2.png)

1. Please click on **Accept** to grant permission to access your subscription.

1. Click **Choose Template**.

    ![](images/lab1-image3.png)

1. Select the **eShopOnWeb** template and click on **Select Template**.

    ![](images/lab1-image4.png)

1. Provide a project name, **eShopOnWeb**, and choose your **Organization**, then click on **Create Project** and wait for the process to complete.

   ![](images/lab1-image5.png)

1. Once the process is complete, click on **Navigate to the project**.

   ![](images/lab1-image6.png)


### Task 2: Enable Advanced Security from Portal

GitHub Advanced Security for Azure DevOps includes extra permissions for more levels of control around Advanced Security results and management. Be sure to adjust individual permissions for your repository.

To ensure Azure DevOps Advanced Security is enabled in your organization, you can follow these steps:

1. Navigate back to the **Azure DevOps** tab, and select **Azure DevOps (1)**.

    ![setup](images/azuredevops.png)

1. Select **eShopOnWeb (2)** project and click on **Project Settings** available in the lower left corner. In the left menu area under Repos, click **Repositories**.

1. Click on the **eShopOnWeb** repository.

1. Click on **Settings**, then click on **Advanced Security**, to turn it on.

    ![setup](images/last2.png)

1. Click **Begin Billing**.

    ![](images/lab1-image12.png)

1. Advanced Security and Push Protection are now enabled. You can also onboard Advanced Security at [Project-level](https://learn.microsoft.com/en-us/azure/devops/repos/security/configure-github-advanced-security-features?view=azure-devops&tabs=yaml#project-level-onboarding) and [Organization-level](https://learn.microsoft.com/en-us/azure/devops/repos/security/configure-github-advanced-security-features?view=azure-devops&tabs=yaml#organization-level-onboarding) as well.
 
### Task 3: Setup Advanced Security permissions

In this task, you will configure advanced security permissions for the eShopOnWeb repository in Azure DevOps. This involves granting specific permissions to project administrators to manage security alerts and settings related to the repository.

1. In the lower-left corner, click on **Project Settings**. In the left menu area under the **Repos** section, click **Repositories**.

      ![](images/lab1-image13.png)
  	
1. Click on the **eShopOnWeb** repository.

1. Select **Security** and click on **Project Administrators**.

1. Next to Advanced Security: manage and dismiss alerts, click the **dropdown**, and select  **Allow**.

1. Next to Advanced Security: manage settings, click the **dropdown**, and select **Allow**.

1. Next to Advanced Security: view alerts, click the dropdown, and select **Allow**.

      ![allow-permissions](images/last1.png)

1. Make sure a green checkmark ✅ appears next to the selected permission.

## Create Work item

You can follow these steps to create a work item to link while committing the changes.

1. Navigate to the **eShopOnWeb** project and select **Boards** from the left menu and select **Work items**

      ![allow-permissions](images/nls3.png)

1. On the **Work items** page, select **+New Work Item** and select **Issue** from the drop-down menu.

    ![allow-permissions](images/nls5.png)

1. Enter **Advanced security related events** in the Title box

1. Enter **Work item to link for all the commits related to Advanced security events** in the description box and click on **Save**

    ![allow-permissions](images/nls4.png)

## Install extension

You can follow these steps to install an extension which is needed in upcoming tasks.

1. Select the **Marketplace** icon from the top right corner and select **Browse marketplace** from the list

    ![allow-permissions](images/ext1.png)

1. On the **Marketplace** window, under **Azure DevOps** search and select **replace tokens**

    ![allow-permissions](images/ext2.png)

1. Select **Get it free**

    ![allow-permissions](images/ext3.png)

1. Select **Install**

     ![allow-permissions](images/ext4.png)

1. Select **Proceed to Organization** to navigate back to **Azure DevOps**

    ![allow-permissions](images/ext5.png)

### Task 4: Viewing alerts of repository 

The Advanced Security Alert Hub is where all alerts are raised and where we gain insights, specifically under the category of Secrets. When a secret is found, you can click on it to access more information. The secret may be located in different places, including various commits. 

1. Under **eShopOnWeb** project, go to the **Repos** tab and click on the **Advanced Security** menu item at the bottom.

   ![setup](images/lab1-image16.png)

1. Click on **Secrets** to see a list of all the exposed secret alerts that have been found. This includes the alert and introduced dates. Click on the **Microsoft Azure Storage account access key identifiable...** to see more details about the alert and what you can do to clean up the secret.

   ![Secrets page](images/advsecurity2.png)

1. Notice that this includes the Recommendation, Locations found, Remediation steps, Severity, and the Date it was first introduced. We can easily clean this up and dismiss the alert.

   ![Secret Details](images/advsc3.png)

### Task 5: Fixing secret scanning alerts

Once a credential touches the repo, it's too late. Hackers might have already exploited it. The only way forward is to permanently eliminate these leaks and find all the places they're being used in production.

 **Note:** Good news! GHAzDO focuses on preventing this in the first place. Bad news! These need to be manually fixed. There isn't an easy button.

#### Push Protection

Push Protection helps protect your repository by preventing unauthorized or malicious code from being pushed to your repository's branches.

#### Updating Secrets:

You can follow these steps to update a file. 

1. While viewing the alert details, click on the line of code, _Constants._ _cs_.

    ![Click on File](images/advsc9.png)

1. Click on **Edit** to edit the file. This will open the code editor and highlight the exact location of the secret. In this case, it's in the .cs file.

   ![setup](images/lab1-image17.png)

1. On line 9, update the variable name to "STORAGE_ID" and click on **Commit** to save changes.
    
     ![setup](images/lab1-image14.png)

1. Enter **StorageDetails** for the branch name and check **Create a pull request**, then click on **Commit** again.

     ![setup](images/lab1-image15.png)

1. The commit was rejected because the repository has secret protection enabled. This is a good thing! It's preventing us from checking in on the exposed secret. Let's fix this.
   
    ![Commit Rejected](images/commit_rejected.png)

    > **Note:** The code went up to the server, was analyzed, rejected, and not stored anywhere. Using Secret push scanning, it catches secrets right before they become a problem.

    > **ProTip!** This can't happen during a Pull Request. Once the code has been pushed into a topic branch, it's too late. PR analysis is best for dependency scanning but not secret push scanning. They are different.

#### Bypass push protection

1. Update your comment with **skip-secret-scanning:true** and click **Commit**.

    ![Commit Bypass](images/commit_bypass2.png)

    >**Note:** Bypassing flagged secrets isn't recommended because bypassing can put a company’s security at risk. 

1. It will give an option to **Create a Pull request**.

    ![Commit Bypass](images/commit_bypass1.png)

#### Fixing Exposed Secrets

You can follow these steps to fix the exposed secret. 

1. Click on **Edit**.

    > **Note**: This scenario is all too common. A developer is testing an application locally and needs to connect to a database, so what do they do? Of course, just put the connection string in the appsettings.json file. They forget to remove it before checking in the code. Now, the secret is exposed in the repo, not just the tip. The exposed credentials will still be in history. This is a huge security hole!

1. On line 9, copy the **STORAGE_ID value** and note it down in a notepad. Now replace this value with **#{STORAGE_ID}#**.

    ![setup](images/lab1-image18.png)

1. Click on **Commit** to save changes. Enter **SecretFix** for the branch name and link the **Work item** created earlier from the list.

    ![Remove STORAGE_ID](images/advsc66.png)

    > **Note:** This step is necessary since the main branch is protected by a pull request pipeline.

1. Next, we need to update the build pipeline to add a variable. Click on **Pipelines** and select **eShoponWeb**.

    ![setup](images/lab1-image19.png)

1. Click on **Edit** to edit the pipeline. Change to the **SecretFix** branch.

     ![setup](images/lab1-image20.png)
   
     ![Remove STORAGE_ID](images/advsc44.png)
 
1. Click on **Variables** and click on **+** New Variable. 

     ![setup](images/lab1-image21.png)

1. Enter **STORAGE_ID** for the name and paste the secret value from Notepad into the value field. Click on **Keep this value secret to hide the value**, then click **OK** and **Save**. Next, we need to edit the pipeline and add a new build task to replace the **#{STORAGE_ID}#** with the actual value.

   ![setup](images/lab1-image22.png)
   
1. While still in edit mode, add the following task between the Checkout and Restore tasks around line 17. This task will replace the **#{STORAGE_ID}#** with the actual value in the **'src/Web/Constants.cs'** file and also remove the tasks related to test and production deployments (Delete the code from line 79) from the existing pipeline, which is not required in our scenario.

    ``` YAML

    - task: qetza.replacetokens.replacetokens-task.replacetokens@6
      inputs:
        targetFiles: '**/*.cs'
        encoding: 'auto'
        tokenPattern: 'custom'
        tokenPrefix: '#{' 
        tokenSuffix: '}#' 
        verbosity: 'detailed' 
        keepToken: false 
    ```
    
    ![Replace Token Task](images/advlab23.png)

1. The final pipeline should look as below:

   ```YAML
    trigger:
    - main

    pool:
      vmImage: ubuntu-latest
    
    extends: 
      template: template.yaml
      parameters:
        stages:
          - stage: Build
            displayName: 'Build'
            jobs:
            - job: Build
              steps:
              - checkout: self
    
              - task: qetza.replacetokens.replacetokens-task.replacetokens@6
                inputs:
                  targetFiles: '**/*.cs'
                  encoding: 'auto'
                  tokenPattern: 'custom'
                  tokenPrefix: '#{' 
                  tokenSuffix: '}#' 
                  verbosity: 'detailed' 
                  keepToken: false
              - task: DotNetCoreCLI@2
                displayName: Restore 
                inputs:
                  command: restore
                  projects: '**/*.csproj'
    
              - task: ms.advancedsecurity-tasks.codeql.init.AdvancedSecurity-Codeql-Init@1
                condition: and(succeeded(), ne(variables['Build.Reason'], 'PullRequest'))
                displayName: 'Initialize CodeQL'
                inputs:
                  languages: csharp
                  querysuite: default
    
              - task: DotNetCoreCLI@2
                displayName: Build
                inputs:
                  projects: '**/*.csproj'
                  arguments: '--configuration $(BuildConfiguration)'
    
              - task: ms.advancedsecurity-tasks.dependency-scanning.AdvancedSecurity-Dependency-Scanning@1
                condition: and(succeeded(), ne(variables['Build.Reason'], 'PullRequest'))
                displayName: 'Dependency Scanning'
    
              - task: ms.advancedsecurity-tasks.codeql.analyze.AdvancedSecurity-Codeql-Analyze@1
                condition: and(succeeded(), ne(variables['Build.Reason'], 'PullRequest'))
                displayName: 'Perform CodeQL analysis'
    
              - task: ms.advancedsecurity-tasks.codeql.enhance.AdvancedSecurity-Publish@1
                condition: and(succeeded(), ne(variables['Build.Reason'], 'PullRequest'))
                displayName: 'Publish Results'
    
              - task: DotNetCoreCLI@2
                displayName: Test
                inputs:
                  command: test
                  projects: '[Tt]ests/**/*.csproj'
                  arguments: '--configuration $(BuildConfiguration) --collect:"Code coverage"'
    
              - task: DotNetCoreCLI@2
                displayName: Publish
                inputs:
                  command: publish
                  publishWebProjects: True
                  arguments: '--configuration $(BuildConfiguration) --output $(build.artifactstagingdirectory)'
                  zipAfterPublish: True
    
              - task: PublishBuildArtifacts@1
                displayName: 'Publish Artifact'
                inputs:
                  PathtoPublish: '$(build.artifactstagingdirectory)'
                condition: succeededOrFailed()
              
    ```
   
1. Select **Validate and save**, and ensure that the check box is marked at commit directly to the **SecretFix** branch setting, then click on **Save**.

    ![Pipeline Save](images/advlab21.png)

1. Once the commit is saved, click on **Repos**, click **Pull Requests**, and click on **New pull request** to merge the changes from branch **SecretFix** into branch **main**. 

1. For the title, enter the **Fixed secret** and click on **Create**. This will run the **eShoponWeb** pipeline to validate changes. 

    ![Pipeline Save](images/nls12.png)

    >**Note:** Make sure you add a random workitem link from the dropdown if it is not added automatically for the pipeline to run successfully.

1. Once the **eShoponWeb** pipeline has been created, click **Approve** and then click on **Complete**.

1. Change **Merge Type** to **Squash commit** and check the box **Delete SecretFix after merging**, to merge changes into the main branch.

    ![Completing merge](images/advlab25.png)

### Task 1: Setup Code Scanning

Code scanning in GitHub Advanced Security for Azure DevOps lets you analyze the code in an Azure DevOps repository to find security vulnerabilities and coding errors. Any problems identified by the analysis are raised as an alert. Code scanning uses CodeQL to identify vulnerabilities.

1. Select the pipeline **eShopOnweb**.

   ![alert_detected](images/advlab33.png)

1. Locate the tasks related to **Advanced Security Code Scanning** that are already included in the YAML pipeline file.

   ![alert_detected](images/nls6.png)
 
1. Do not run the pipeline. The code scanning setup has already been initiated, along with dependency scanning performed in the previous lab.

### Task 2: Review Code Scanning Alert (Gain Insights)

1. Go to the **Repos** tab and click on the **Advanced Security** menu at the bottom.

1. Click on **Code scanning** to see a list of all the code scanning alerts that have been found. This includes the alert, vulnerable code details, and first detected date.

#### Code scanning Alert Details

1. Click on the item ***Uncontrolled command line...*** to see the details about this alert.

1. This includes the Recommendation, Locations found, Description, Severity, and the Date it was first detected. We can easily fix this threat. 

   ![code_alert_detected](images/nls7.png)

1. You can also view the code that triggered the alert and what build detected it.
   
1. Click on **Detections** to see the different builds that detected this alert.

   ![where_detected](images/nls81.png)

    **ProTip!** When a vulnerable component is no longer detected in the latest build for pipelines with the dependency scanning task, the state of the associated alert is automatically changed to Closed. To see these resolved alerts, you can use the **State filter** in the main toolbar and select **Closed**.

### Task 3: Fixing the Code to resolve the alert

1. This is simple to fix using parameters in the dynamic SQL described in the remediation steps.

1. Click on **Locations found** to see the code that triggered the alert.

   ![Image](images/advlab4n6.png)

1. Click on the **Edit** button to edit the file. Line number 23 is highlighted here. 

1. The value of __{drive}__ is getting highlighted from line number 23.

    ![Image](images/nls9.png)

1. Instead of getting the value of 
__{drive}__ using a query, we can directly define it as __C__ for the string drive variable in the line 20.
    ```C#
    string drive = "C";
    ```

    ![Image](images/nls11.png)

1. Click on **Commit** to save changes. Enter **Fixalert** for the branch name and link the work item. Check **Create a pull request**, and then click on **Commit** again.

    ![Image](images/nls10.png)

    > **Note:** This step is necessary since the main branch is protected by a pull request pipeline.

1. Navigate to Azure DevOps, click on **Repos**, select **Pull requests** and select **Create a pull request** to push the commits from **Fixalert** to the **main**.

1. On the **New pull request** page, click on **Create**.

    ![Image](images/mls3.png)

1. Once the **eShoponWeb** pipeline has been completed, click on **Approve** and then click on **Complete**.

    ![Image](images/mls4.png)

1. Change **Merge Type** to **Squash commit** and check the box **Delete Fixalert after merging** to merge changes into the main branch.

    ![Image](images/mls5.png)

    > **Note**: The build will run automatically, initiating the code scanning task and publishing the results to Advanced Security.

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


 