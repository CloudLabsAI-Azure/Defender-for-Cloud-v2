# **Lab 5 : Defender for DevOps**

In this lab, we'll explore integrating GitHub Advanced Security (GHAS) and Microsoft Defender for DevOps into your CI/CD pipeline to enhance security. We’ll focus on using GHAS and Defender for DevOps to automatically detect and address security issues within your pipeline. Instead of solely relying on traditional security measures, we’ll configure these tools to provide continuous security assessments and proactive responses directly within your DevOps environment.

## Estimated Duration: 180 Minutes

## Lab objectives :

In this lab, you will complete the following tasks:

- Task 1: Understanding CI/CD pipelines in Azure DevOps
- Task 2: Identifying security issues in the pipeline
- Task 3: Overview of GitHub Advanced Security (GHAS) [Read-Only]
- Task 4: Overview of Defender for DevOps (including pricing) [Read-Only]
- Task 5: Securing your pipeline with GHAS
- Task 6: Connecting and Securing your Azure DevOps environment to MDC
- Task 7: Role of Defender Cloud Security Posture Management (DCSPM)

## **Task 1: Understanding CI/CD pipelines in Azure DevOps**

Continuous Integration and Continuous Deployment (CI/CD) are critical practices in modern software development, enabling teams to deliver code changes more frequently and reliably. Azure DevOps provides a robust platform to implement CI/CD pipelines. Here’s a basic understanding and a simple pipeline example to get you started.

### CI/CD Pipelines in Azure DevOps

**Continuous Integration (CI):**
CI involves automatically building and testing your code every time a team member commits changes to version control. The key goals are to detect errors quickly and to improve software quality.

**Continuous Deployment (CD):**
CD extends CI by automatically deploying all code changes to a production environment after the build and test stages are successful. This ensures that the software is always in a releasable state.

### 1. Components of a CI/CD Pipeline

1. **Source Control:** Where the code resides (e.g., Git repository).
2. **Build:** The process of compiling the source code into executable artifacts.
3. **Test:** Automated tests run to validate the code.
4. **Release:** Deploying the artifacts to staging or production environments.

### 2. Set up an Azure DevOps organization.

1. On your lab VM open **Edge Browser** on desktop and navigate to [Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137), and if prompted sign with the credentials.

   * Email/Username: <inject key="AzureAdUserEmail"></inject>

   * Password: <inject key="AzureAdUserPassword"></inject>

2. If prompted click **Ask later** on **Action Required** page.

3. On the next page accept defaults and click on continue.
   
   ![](images/AZ-400-odl.png)
   
4. On the **Almost Done...** page fill in the captcha and click on continue. 

   ![](images/AZ-400-almost.png)
    
5. On the Azure DevOps page click on **Azure DevOps** located at top left corner and then click on **Organization Setting** at the left down corner

   ![Azure DevOps](images/az-400-lab3-(1).png)
    
6. In the **Organization Setting** window on the left menu click on **Billing (1)** .

   ![Azure DevOps](images/az-400-lab3-1.png)

1. Select **Setup Billing (2)** then click on **save (3)**.

   ![Azure DevOps](images/az-400-lab3-2.png)    

7. On the **MS Hosted CI/CD (1)** section under **Paid parallel jobs** enter value **1** and at the end of the page click on **Save (2)**.

   ![Azure DevOps](images/az-400-lab3-3.png)

1. On the **Organization Settings** page, go to the Security section and click **Policies** (1). Enable the toggles for both **Third-party application access via OAuth** (2) and **Allow public projects** (3).

   ![](images/nls1.png)

1. In the **Organization Settings** window, click on **Settings** in the left menu. On the settings page, toggles for **Creation of Classic Release Pipeline** option.

   ![](images/21.png)

1. Navigate to **azuredevopsdemogenerator** using the link below. This utility site will automate the process of creating a new Azure DevOps project within your account that is prepopulated with content (work items, repos, etc.) required for the lab. For more information on the site, please see [https://docs.microsoft.com/en-us/azure/devops/demo-gen](https://docs.microsoft.com/en-us/azure/devops/demo-gen).

   ```
   https://azuredevopsdemogenerator.azurewebsites.net/
   ```
  
1. Click on **Sign in** and log in using the Microsoft account associated with your Azure DevOps subscription.

   ![](images/lab1-image2.png)

1. Please click on **Accept** to grant permission to access your subscription.

1. Click **Choose Template**.

   ![](images/lab1-image3.png)

1. Select the **eShopOnWeb (1)** template and click on **Select Template (2)**.

   ![](images/lab1-image4.png)

1. Provide a project name, **eShopOnWeb (1)**, and choose your **Organization (2)**, then click on **Create Project (3)** and wait for the process to complete.

   ![](images/lab1-image5.png)

1. Once the process is complete, click on **Navigate to project**.

   ![](images/lab1-image6.png)

1. Click on **Repos** > **Branches**, then click the ellipsis next to your branch and select **Branch security**.

   ![](images/167.png)

1. Select **Project Administrators** and set **Bypass policies when pushing** to **Allow**.

   ![](images/166.png)

### 3. Create a Simple HTML File

1. Click on **Repos (1)>Files (2)**, Click on the three-dot menu (`...`) in the top-right corner of the repository's file explorer section. This menu provides additional actions you can take within the repository.

1. After clicking the three-dot menu, a dropdown list appears. From this list, hover over the **New** option. This will allow you to create new items in the repository.

1. Under the **New** submenu, you have the option to create a "File" or a "Folder." In this case, you would click on **File** to start creating a new file within the repository.

   ![](images/89.png)

1. A prompt will appear asking you to name your new file.

2. Enter a name for your HTML file, such as `index.html`.

   ![](images/23.png)

1. Add the following content to `index.html` file:

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

1. After writing your code, you can commit the changes by filling in the commit message and selecting whether to commit directly to the main branch or a different one.

1. Click **Commit** to save the file to your repository.

1. Navigate back to the **Pipelines** pane in of the **Pipelines** hub.

1. In the **Pipelines** window, click **New pipeline**.

   > **Note**: We will use the wizard to create a new YAML Pipeline definition based on our project.

1. On the **Where is your code?** pane, click **Azure Repos Git (YAML)** option.

   ![](images/10.png)

1. On the **Select a repository** pane, click **eShopOnWeb**.

   ![](images/12.png)

1. On the **Configure your pipeline** pane, scroll down and select **Starter pipeline**.

   ![](images/164.png)

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
   
8. Click **Save and Run** then click **Save and Run** to start the Build Pipeline process.

   ![](images/14.png)

9. Wait for the Build Pipeline to complete successfully.

   > **Note**: Each task from the YAML file is available for review, including any warnings and errors.

### 4. Create a Release Pipeline

1. Go to **Pipelines** > **Releases** > **New pipeline**.

   ![](images/24.png)

1. From the **Select a template** window, **choose** **Azure App Service Deployment** (Deploy your application to Azure App Service. Choose from Web App on Windows, Linux, containers, Function Apps, or WebJobs) under the **Featured** list of templates.    

1. Click **Apply**.

   ![](images/33.png)

1. Click on **Add an Artifact(1)** tab and select the **eShopOnWeb (2)**  in the Source (build pipeline) field. Click **Add (3)** to confirm the selection of the artifact.

   ![](images/artifact1.png)

1. On the All pipelines > New Release Pipeline pane, Click on **Tasks** tab and ensure that the **Stage 1** is selected. In the **Azure subscription(2)** dropdown list, Confirm the App Type is set to **Web App on Windows(3)**. Next, in the App Service name dropdown list, select the name of the **asclab-app (4)** web app.

   ![](images/stage.png)

   >**Note**: After Selecting your Azure subscription click Authorize. If prompted, authenticate by using the user account with the Owner role in the Azure subscription

1. Select the Task **Deploy Azure App Service**. In the **Package or Folder** field, Select Package or Folder. it shoud look like `"$(System.DefaultWorkingDirectory)/_eShopOnWeb (2)/html-artifact"`

   ![](images/27.png)

1. On the **All pipelines > New Release Pipeline** pane, click **Save** and, in the **Save** dialog box, click **OK**.

   ![](images/34.png)

1. On the **All pipelines > New Release Pipeline** pane, click **Create release** then click on **Create**.

   ![](images/29.png)

1. Wait until the release pipeline is completed.

   ![](images/30.png)

1. Navigate to the Azure portal interface, navigate to the resource group **asclab**, and in the list of resources, click the **asclab-app-xxxxxxxxxxxxx** web app.

1. On the web app blade, click **Browse**.

   ![](images/32.png)

1. Verify that the web page loads successfully in a new web browser tab.

   ![](images/31.png)

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If you receive a success message, you can proceed to the next task.
   - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
 
<validation step="d1bea6eb-b9ab-4f67-8bbd-f90e80f58b8e" />

## **Task 2: Identifying security issues in the pipeline** 

Integrate Microsoft Security DevOps into your Azure DevOps pipeline to scan Infrastructure as Code (IaC) templates for security issues. This process ensures early detection of vulnerabilities, enhances compliance and improves your overall security posture within existing DevOps workflows.

1. Click on **Repos (1)>Files**, Click on the three-dot menu (`...`) in the top-right corner of the repository's file explorer section. This menu provides additional actions you can take within the repository.

1. After clicking the three-dot menu, a dropdown list appears. From this list, hover over the **New** option. This will allow you to create new items in the repository.

1. Under the **New** submenu, you have the option to create a "File" or a "Folder." In this case, you would click on **Folder** to start creating a new Folder within the repository.

1. A prompt will appear asking you to name your Folder and File.

2. Enter a name for your Folder, such as `terraform` and file name such as `ask.tf`.

   ![](images/lab15-8.png)

1. Add the following content to `ask.tf` file:

   ```
   resource azurerm_kubernetes_cluster "k8s_cluster" {
   dns_prefix          = "terragoat-${var.environment}"
   location            = var.location
   name                = "terragoat-aks-${var.environment}"
   resource_group_name = azurerm_resource_group.example.name
   identity {
     type = "SystemAssigned"
   }
   default_node_pool {
     name       = "default"
     vm_size    = "Standard_D2_v2"
     node_count = 2
   }
   addon_profile {
     oms_agent {
       enabled = false
    }
     kube_dashboard {
      enabled = true
     }
   }
   role_based_access_control {
     enabled = false
   }
   tags = {
     git_commit           =  "898d5beaec7ffdef6df0d7abecff407362e2a74e"
     git_file             = "terraform/azure/aks.tf"
     git_last_modified_at = "2020-06-17 12:59:55"
     git_last_modified_by = "nimrodkor@gmail.com"
     git_modifiers        = "nimrodkor"
     git_org              = "bridgecrewio"
     git_repo             = "terragoat"
     yor_trace            =  "6103d111-864e-42e5-899c-1864de281fd1"
    }
   }
   ```
1. After writing your code, you can commit the changes by filling in the commit message and selecting whether to commit directly to the main branch or a different one.

1. Click **Commit** to save the file to your repository.

1. Navigate back to **teraform** folder and Click on the three-dot menu (`...`) in the top-right corner of the repository's file explorer section.

1. Click on the three-dot menu to open a dropdown list. From the list, select the **Upload file(s)** option to upload files to the repository.

    ![](images/lab5-1.png)

1. Click the **Browse** button in the Commit window to select files for upload.

   ![](images/lab5-2.png)

1. Navigate to the `C:\Allfiles\terraform-file` folder, select all files, and click **Open**.

1. Click on **Commit** button in the Commit window to select files for upload.

   ![](images/lab5-3.png)

1. Navigate to **Github** using the link below. 

   ```
   https://github.com/login
   ```
1. In the sign-in to GitHub page in edge browser, enter the **GitHub UserEmail** and **GitHub Password** and click on **Sign in**.

   ![](images/lab5-4.png)

   >**Note**: Navigate to the **Environment** tab to view the key-value pairs of the **GitHub UserEmail**, and **GitHub Password** in the **license** section. You can use the copy buttons under the actions column to have the values copied instantly. Alternatively, it is suggested to have the values copied over onto a notepad for easy accessibility. 

     ![](images/158.png)

1. Open a new tab and navigate to `https://www.microsoft.com/en-us/microsoft-365/outlook/log-in` then click on **Sign-in**.

   ![](images/137.png)

1. You'll see the **Sign into Microsoft Outlokk** tab. Here, enter your credentials:
 
   - **Email/Username:** 
 
       ![Enter Your Username](images/138.png)
 
1. Next, provide your password:
 
   - **Password:** 
 
      ![Enter Your Password](images/139.png)

1. The email containing the verification code can sometimes creep into the archive/spam folders within your Outlook. Copy the verification code.

   ![Enter Your Password](images/140.png)

1. Navigate back to the **Device Verifition** page, enter the code you copied and click on **Verify**.

   ![](images/141.png)

1. Under Dashboard, click **New** next to **Top repositories**.

   ![](images/lab5-9.png)

1. On the **Create a new repository** page, set the repository name to **defender-for-cloud**.

   ![](images/lab5-8.png)

1. Scroll down and select **Create repository**.

   ![](images/lab5-10.png)

1. Copy the repository link to Notepad, as you'll need it to complete this task, and then click on **Creating a new file**.

   ![](images/lab5-7.png)

1. Enter the name **defender-for-cloud.tf** and click on **Commit changes**.

   ![](images/lab5-6.png)

1. Select **Commit changes**.

   ![](images/lab5-5.png)

1. Click on your profile picture in the top right corner.

1. Select **Settings** from the dropdown menu.

   ![](images/lab5-26.png)

1. Scroll down in the left sidebar and click on **Developer settings.**

   ![](images/lab5-25.png)

1. In the Developer settings, click on **Personal access tokens.**

1. You may see two options: **Tokens (classic)** and **Fine-grained tokens** Click on **"Tokens (classic)"**.

   ![](images/lab5-24.png)

1. Click on the **Generate new token** button and select **Generate new token (Classic)**.

   ![](images/lab5-23.png)

1. **Configure Token Settings:**
   - **Token name**: Enter **mypattoken**
   - **Expiration**: Set an expiration for your token to 30 days.
   - **Select Scopes**: Choose the scopes (permissions) for your token:
     - select `repo`, `write:packages`, `admin:org`

1. Once you've configured everything, click on the **Generate token** button at the bottom of the page.

1. After generating, your new token will be displayed. **Copy it immediately** to the notepad as you won't be able to see it again. Store it in a secure place (e.g., password manager).

1. Return to Azure DevOps, click on **eShopOnWeb (1)**, and then select **Clone (2)**.

   ![](images/lab5-11.png)

1. Copy the repository link to the notepad you will be needing to complete this task.

   ![](images/lab5-12.png)

1. In your labvm, search and select powershell and run the following command to clone your Azure DevOps repository:

   ```bash
   git clone <azuredevopsrepo>
   ```

   >**Note**: Replace <azuredevopsrepo> with repository link you copied in the previous step.  

1. Enter **Email/Username**: <inject key="AzureAdUserEmail"></inject>, then click on **Next**.

    ![](images/lab5-13.png)

1. Enter **Password**: <inject key="AzureAdUserPassword"></inject>, then click on **Sign in**.

   ![](images/lab5-14.png)

1. Navigate to the directory:

   ```bash
   cd eShopOnWeb
   ```
2. **Navigate to the Terraform Folder:**
   Check the contents of the repository and navigate to the Terraform folder:

   ```bash
   cd terraform
   ```
3. **Copy the Files:**

   You can copy the files you want to share from the Terraform folder to a separate location on your machine. For example, create a new directory for the GitHub repo:

   ```bash
   mkdir ~/defender-for-cloud-files
   cp * ~/defender-for-cloud-files/   # Copies all files
   ```

   ![](images/lab5-15.png)

4. Open a new PowerShell window and run the command to clone the GitHub repository:

   ```bash
   git clone <azuregithubrepo>
   ```
   >**Note**: Replace <azuregithubrepo> with repository link you copied in the previous steps.  

1. Navigate to the GitHub repo directory:

   ```bash
   cd defender-for-cloud
   ```
1. Copy the files from the Azure DevOps Terraform folder to the GitHub repo:

   ```bash
   cp ~/defender-for-cloud-files/* .   # Copies all files to the current directory
   ```
1. Set your global user email:

   ```bash
   git config --global user.email "you@example.com"
   ```
   >**Note**: Replace **you@example.com** with your GitHub email, for example, `github_cloudlabsuser_101@cloudlabsaiuser.com`.

1. Set your global user name:

   ```bash
   git config --global user.name "Your Name"
   ```
   >**Note**: Replace **Your Name** with your GitHub username, for example, `github-cloudlabsuser-101`.

1. Check the current configuration :

   ```bash
   git config --list
   ```
1. To add files to GitHub, use the following command:
 
   ```bash
   git add .
   ```
1. After setting your user information, you can commit changes with:

   ```bash
   git commit -m "Added new Terraform files"
   ```
1. Finally, push your changes to the remote repository (replace `origin` and `main` with your specific remote and branch names if needed):

   ```bash
   git push origin main
   ```

   **Note:** If prompted to connect to GitHub, select **token**, paste the token you copied earlier, and then click **Sign in**.

   ![](images/lab5-17.png)

1. Go to the GitHub repository to access all the available files.

   ![](images/lab5-16.png)

1. On your lab computer, open the Azure DevOps portal with the **eShopOnWeb** project in a web browser. Click on the **marketplace icon > Browse Marketplace**.

   ![](images/61.png)

1. Search for **Microsoft Security DevOps** in the Marketplace and open it.

   ![](images/62.png)

1. On the **Microsoft Security DevOps** page, click on **Get it for free**.

   ![](images/63.png)

1. Select the desired Azure DevOps organization and **Install**.

   ![](images/64.png)

1. Click **Proceed to organization** once the installation is complete.

   ![](images/65.png)

1. Navigate to the **Pipelines** pane in the **Pipelines** hub.

1. Click **New pipeline** in the **Pipeline** window.

1. Select **Azure Repos Git (YAML)** on the **Where is your code?** pane.

   ![](images/10.png)

1. Click **eShopOnWeb** on the **Select a repository** pane.

   ![](images/12.png)

1. Scroll down and select **Starter pipeline** on the **Configure your pipeline** pane.

   ![](images/164.png)

1. Define your build pipeline in a file named `azure-pipelines.yml` with the following content:

   ```yaml
   trigger:
   - main

   pool:
     vmImage: ubuntu-latest

   steps:
   - task: MicrosoftSecurityDevOps@1
     displayName: 'Microsoft Security DevOps'
     inputs:    
      categories: 'IaC'
   ```
1. This YAML file is designed to configure an Azure DevOps pipeline with the following specifics:

   - **Trigger**: The pipeline is set to `none`, meaning it won't run automatically based on code changes or other triggers. It will need to be started manually or triggered by another pipeline or service.
   - **Pool**: Specifies that the pipeline will use the `windows-latest` virtual machine image. This VM image is provided by Azure DevOps and ensures the pipeline runs on a fresh Windows environment with the latest updates.
   - **Steps**: The pipeline includes a single step:
     - **Task**: Uses the `MicrosoftSecurityDevOps@1` task, which is designed for Microsoft Security DevOps.
     - **Display Name**: The task is labeled 'Microsoft Security DevOps' for easy identification in the pipeline UI.
     - **Inputs**:
       - `categories`: Set to 'IaC', indicating that the task will focus on Infrastructure as Code (IaC) security scanning.

8. Click **Save and Run** then click **Run** to start the Build Pipeline process.

   ![](images/68.png)

9. Wait for the build pipeline to complete. Once finished, review the results, including any warnings or errors reported by the tasks. Address any issues identified, make the necessary changes, and then rerun the pipeline to ensure that all problems are resolved.
   
   ![](images/69.png)

### Benefits of Using Microsoft Security DevOps for Scanning the Pipeline

1. **Early Detection of Security Issues**: Microsoft Security DevOps integrates security scanning directly into your CI/CD pipeline, ensuring that security issues in your IaC templates are identified early in the development process.

2. **Enhanced Compliance**: By incorporating automated security checks, your pipeline remains compliant with industry standards and best practices, reducing the risk of deploying insecure infrastructure.

3. **Improved Security Posture**: Continuous scanning and monitoring of your IaC configurations help maintain a robust security posture by promptly addressing vulnerabilities and misconfigurations.

4. **Streamlined Security Workflows**: Integrating security tools into your existing DevOps workflows simplifies the process of managing and mitigating security risks, reducing manual intervention and enhancing efficiency.

5. **Comprehensive Security Coverage**: Microsoft Security DevOps covers a wide range of security aspects, including code analysis, dependency scanning, and configuration checks, providing comprehensive security coverage for your pipeline.

Here's a detailed and updated version of the overview for GitHub Advanced Security (GHAS), broken down step by step:

## **Task 3: Overview of GitHub Advanced Security (GHAS) [Read-Only]**

### Overview of GitHub Advanced Security (GHAS)

GitHub Advanced Security (GHAS) is a suite of security tools built into the GitHub platform designed to help developers secure their code and workflows. It includes features such as code scanning, secret scanning, and dependency review to identify and remediate security vulnerabilities and exposures.

1. Enable GitHub Advanced Security
To use GHAS, you need to have GitHub Advanced Security enabled for your repository. This typically requires a GitHub Enterprise subscription.

   - Navigate to your repository on GitHub.
   - Click on `Settings`.
   - In the `Security` section, find `GitHub Advanced Security` and enable it.

2. Configure Code Scanning

   **Code scanning** helps detect vulnerabilities and errors in your code by running static analysis tools.

   - Go to the `Security` tab of your repository.
   - Click on `Set up code scanning`.
   - You can choose from different options like `CodeQL Analysis`, which is a powerful tool provided by GitHub. Select the `Set up this workflow` button for `CodeQL Analysis`.
   - Review the configuration file (e.g., `.github/workflows/codeql-analysis.yml`). Modify it if needed and commit it to your repository.

3. Run Code Scanning

   - Once configured, code scanning runs automatically on the specified events (like pushes and pull requests).
   - You can also manually trigger a scan by going to the `Actions` tab, finding the `CodeQL` workflow, and clicking `Run workflow`.

4. Review Code Scanning Results

   - Navigate to the `Security` tab, and under `Code scanning alerts`, you'll see a list of detected issues.
   - Click on any alert to get detailed information about the vulnerability and recommended fixes.

5. Configure Secret Scanning

   **Secret scanning** detects secrets (like API keys and tokens) that may have been accidentally committed to your repository.

   - Go to the `Security` tab of your repository.
   - Click on `Set up secret scanning`.
   - GitHub automatically scans for patterns that match common secret types and alerts you if any are found.

6. Review Secret Scanning Results

   - Navigate to the `Security` tab, and under `Secret scanning alerts`, you'll see a list of detected secrets.
   - Click on any alert to view details and follow the steps to revoke or rotate the compromised secrets.

7. Set Up Dependency Review

   **Dependency review** helps you understand and remediate vulnerable dependencies in your project.

   - Ensure your project has a dependency manifest file (e.g., `package.json`, `pom.xml`).
   - GitHub automatically generates dependency graphs and checks for known vulnerabilities in your dependencies.

8. Review Dependency Alerts

   - Navigate to the `Security` tab, and under `Dependency review`, you'll see alerts for vulnerable dependencies.
   - Click on any alert to see details about the vulnerability and recommended versions to update to.

9. Manage Security Policies
  
   - In the `Security` tab, you can also manage security policies by setting up a `security.md` file to inform users about your project's security practices and how they can report vulnerabilities.

10. Continuous Monitoring and Alerts

      - GitHub Advanced Security continuously monitors your repository and generates alerts for any new issues found.
      - Make it a habit to regularly review the `Security` tab and address any new alerts promptly.

## **Task 4: Overview of Defender for DevOps (including pricing) [Read-Only]**

Defender for DevOps is a security solution by Microsoft designed to enhance the security of DevOps environments. It provides a range of tools and features to help secure the software development lifecycle (SDLC) and protect against threats that target DevOps processes. Here's an overview:

### Overview of Microsoft Defender for DevOps

**Microsoft Defender for DevOps** helps secure your DevOps environments and pipelines by integrating with popular DevOps tools and providing security recommendations for your code and infrastructure. It focuses on the following areas:

1. **Source Code Security**: Scans your code repositories to identify vulnerabilities and security issues in your source code.
2. **Pipeline Security**: Monitors and protects your CI/CD pipelines from potential threats and unauthorized access.
3. **Infrastructure as Code (IaC) Security**: Analyzes your IaC templates (e.g., Terraform) for potential security risks and misconfigurations.

**Key Features:**
- **Integration**: Works with tools like GitHub, Azure DevOps, and Bitbucket.
- **Vulnerability Scanning**: Identifies vulnerabilities in your code, dependencies, and configuration files.
- **Security Recommendations**: Provides actionable insights and recommendations for securing your pipelines and code.
- **Compliance**: Helps ensure compliance with industry standards and best practices.

### Pricing

The pricing for Microsoft Defender for DevOps is generally included as part of the broader Microsoft Defender for Cloud offering, specifically under **Defender for Cloud's Cloud Security Posture Management (CSPM)** plan. Here’s a general breakdown:

- **Microsoft Defender for Cloud (Free Tier)**: Basic security features, including Azure Security Center and Azure Defender for servers.
- **Microsoft Defender for Cloud (Standard Tier)**: Includes advanced features such as Defender for DevOps, Defender for Cloud's comprehensive protection, and threat detection capabilities. 

For exact pricing details, especially as they can vary based on the size of the environment and specific needs, it's best to consult the [Microsoft Defender for Cloud pricing page](https://azure.microsoft.com/en-us/pricing/details/defender-for-cloud/) or contact Microsoft sales for a customized quote.

### Inclusion in Defender for CSPM

**DevOps Security Posture** is included as part of the **Defender for CSPM** plan, which provides a unified view of your security posture across various cloud environments, including DevOps. This integration means that DevOps security capabilities are seamlessly integrated into the overall cloud security management, offering a more comprehensive approach to securing both your cloud infrastructure and development workflows.

## **Task 5: Securing your pipeline with GHAS**

To secure your pipeline with GitHub Advanced Security (GHAS) and Microsoft Defender for DevOps, you can integrate these tools to enhance your pipeline's security posture. Here’s an example of how to use GHAS and Defender for DevOps for security:

#### Enable Advanced Security from Portal

GitHub Advanced Security for Azure DevOps includes extra permissions for more levels of control around Advanced Security results and management. Be sure to adjust individual permissions for your repository.

To ensure Azure DevOps Advanced Security is enabled in your organization, you can follow these steps:

1. Click **Project settings (1)** in the lower-left corner. In the left menu under Repos, click **Repositories (2)**, then select the **eShopOnWeb (3)** repository.

   ![setup](images/06-26-2024(4).png)

1. Click on **Settings (1)**, then click on **Advanced Security (2)**, to turn it **On** and click **Enable all**.

    ![setup](images/06-26-2024(5).png)

1. Click **Begin Billing**.

    ![](images/lab1-image12.png)

1. Advanced Security and Push Protection are now enabled. You can also onboard Advanced Security at [Project-level](https://learn.microsoft.com/en-us/azure/devops/repos/security/configure-github-advanced-security-features?view=azure-devops&tabs=yaml#project-level-onboarding) and [Organization-level](https://learn.microsoft.com/en-us/azure/devops/repos/security/configure-github-advanced-security-features?view=azure-devops&tabs=yaml#organization-level-onboarding) as well but we recommend for this hands-on lab to enable it only for repository level.

#### Create a pull request

1. Navigate to the **Pipelines** pane in the **Pipelines** hub.

1. Click **New pipeline** in the **Pipeline** window.

1. Select **Azure Repos Git (YAML)** on the **Where is your code?** pane.

   ![](images/10.png)

1. Click **eShopOnWeb** on the **Select a repository** pane.

   ![](images/12.png)

1. Scroll down and select **Starter pipeline** on the **Configure your pipeline** pane.

   ![](images/164.png)

1. Define your build pipeline in a file named `azure-pipelines.yml` with the following content:

   ```
   trigger:
    - main
    
   pool:
      vmImage: ubuntu latest
    
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
     
1. Click on **Save and Run** and then **Save and Run** .

1. Please navigate to the pipeline and select it. The execution may take around 5 minutes to complete. Kindly wait until the build finishes, then review the pipeline result.

   ![](images/165.png)

## **Task 6: Connecting and Securing your Azure DevOps environment to MDC**

1. Navigate to the Azure portal.

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
   | Resource group | defenderforcloud |
   | Location | East US|

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

   >**Note:** It might take upto 24hrs to reflect the real-time status.

1. Navigate to **DevOps workbook** and change the toggle to **Yes**, which provides an overview of the tabs provided below

   ![alert_detected](images/m55.png)

   ![alert_detected](images/m52.png)

1. Navigate to the **Infrastructure as Code** tab and scroll down, click on the **Severity** section to open the individual findings, and click on **Information** which in turn provides detailed findings and the issue location.

   ![alert_detected](images/121.png)

1. Similarly, navigate to the **Secrete** tab and click on **link** and identify the issues.

   ![alert_detected](images/122.png)

1. Then take note of the recommendations provided to resolve the issues.

   ![alert_detected](images/123.png)

1. Navigate back to **Environment settings** under Management > **+Add environment** > **Github**

   ![alert_detected](images/advlab51.png)

1. On the **Azure DevOps Connection** page, under **Account details**, provide the below settings.

   | Setting  | Value |
   -----------|---------
   | Connector name | Githubconnector **(1)** |
   | Subscription | Choose the default subscription **(2)**  |
   | Resource group | defenderforcloud **(3)**  |
   | Location | East US **(4)** |

   ![alert_detected](images/lab5-20.png)

1. Select **Next: Configure access (5)**

1. Select **Authorize (1)**. Ensure you're authorizing the correct Azure Tenant using the drop-down menu in Azure DevOps and by verifying you're in the correct Azure Tenant in Defender for Cloud.

1. In the popup dialog, read the list of permission requests, and then select **Accept**.

1. Select **Install (2)**.

   ![alert_detected](images/lab5-19.png)

   **Note:** To install Microsoft Security DevOps, follow the prompt to select the GitHub username where you would like the installation to take place.

   ![alert_detected](images/lab5-21.png)

1. Leave all other settings as default.

1. Select **Next: Review and generate (3)**.

1. Review the information, and then select **Create**.

1. You will be prompted to log in to your GitHub account. Authenticate and authorize Microsoft Azure to access your GitHub organization.

1. Once connected, select the GitHub repositories that you want to integrate with Defender for Cloud.

1. Review and confirm the repository connections.

1. After the connection is established, Defender for Cloud will begin monitoring the selected repositories for vulnerabilities and other security issues.

1. In the Microsoft Defender for Cloud portal, go to **Recommendations** under the **Secure Score** tab.

1. Scroll to the **DevOps Security** section. This is where recommendations from GitHub Advanced Security will appear.
   
1. You may see recommendations such as:
     - Code scanning issues from GitHub (e.g., vulnerabilities in code).
     - Secret scanning alerts (e.g., exposed API keys or passwords).
     - Dependency vulnerabilities (e.g., outdated libraries with known vulnerabilities).

1. Each recommendation will contain detailed remediation steps provided by Defender for Cloud. You can click on each issue to get more information about its severity and how to fix it.

1. In your GitHub repository, go to the **Security** tab.

1. You’ll see a list of issues identified by GitHub Advanced Security such as code scanning results, secret scanning alerts, and dependency alerts.

1. Follow the instructions provided in both GitHub and Defender for Cloud to resolve each issue. You may need to:
     - Fix vulnerable code.
     - Rotate or remove exposed secrets.
     - Update vulnerable dependencies to safe versions.

1. As issues are resolved, both GitHub and Defender for Cloud will update the status of the recommendations.

1. To verify everything is set up correctly, push a commit that introduces a known vulnerability (e.g., a hardcoded secret or vulnerable dependency).
   
1. After pushing the commit, check GitHub for security alerts and verify that the same issues appear as recommendations in Defender for Cloud under the **DevOps Security** section.

1. Defender for Cloud will continuously monitor your connected GitHub repositories and provide real-time security recommendations. You can monitor these from the **Secure Score** and **Recommendations** sections.

## **Task 7: Integrating non-MS security scan solutions with MDC**

To enhance your security posture comprehensively, integrating non-Microsoft security scan solutions with Microsoft Defender for Cloud (MDC) is a strategic approach. For a straightforward integration of a security scan into your Azure DevOps pipeline using **Snyk** with minimal configuration, follow these steps:

### Security Scan with Snyk

**Snyk** is a well-known tool for vulnerability scanning in open source dependencies. Here’s how to integrate Snyk into your Azure DevOps pipeline to identify security issues:

1. Navigate to **Snyk's website** using the link below. 

   ```
   https://snyk.io/
   ```
1. **Click on "Start free"** or **"Sign up"** located on the left of the page.
    
   ![](images/71.png)

1. On the sign-up page, **select the "Sign up with GitHub"** option.

   ![](images/72.png)

1. In the sign-in to GitHub page in edge browser, enter the **GitHub UserEmail** and **GitHub Password** and click on **Sign in**.

   ![](images/136.png)

   >**Note**: Navigate to the **Environment** tab to view the key-value pairs of the **GitHub UserEmail**, and **GitHub Password**. You can use the copy buttons under the actions column to have the values copied instantly. Alternatively, it is suggested to have the values copied over onto a notepad for easy accessibility. 

     ![](images/158.png)

1. Open a new tab and navigate to `https://www.microsoft.com/en-us/microsoft-365/outlook/log-in` then click on **Sign-in**.

   ![](images/137.png)

1. You'll see the **Sign into Microsoft Outlokk** tab. Here, enter your credentials:
 
   - **Email/Username:** 
 
       ![Enter Your Username](images/138.png)
 
1. Next, provide your password:
 
   - **Password:** 
 
      ![Enter Your Password](images/139.png)

1. The email containing the verification code can sometimes creep into the archive/spam folders within your Outlook. Copy the verification code.

   ![Enter Your Password](images/140.png)

1. Navigate back to the **Device Verifition** page, enter the code you copied and click on **Verify**.

   ![Enter Your Password](images/141.png)

1. **Authorize Snyk** to access your GitHub account by clicking "Authorize Snyk" on the GitHub authorization page.

   ![](images/135.png)

1. On **where is the code you want to scan** page, select **Skip for now**.

1. Select to **Integrations** from the left pannel and then select on **Azure Repos**.

   ![](images/169.png)

1. On the account credentials page enter your azure devops organization name and enter a personal access token.

   ![](images/170.png)

   >**Note**: Click on **Create a personal access token**, name it **mypattoken**, grant it full access, and then generate the token. Once created, copy the token and paste it into the **Personal Access Token** field on the Snyk page.

1. Navigate to the **Projects** and click on **Add project** then select **Azure Repos**.

   ![](images/171.png)

1. Select **eShopOnWeb** and then click on **Add selected repositories**, wait until the import is completed.

   ![](images/172.png)

1. Navigate to the **Setting > Snyc Code** and make sure **Enable Snyk code** is enable.

   ![](images/168.png)

1. Navigate to the **Setting > Genaral** and scroll down and record Organization ID it in your notepad.

   ![](images/lab15-9.png)

1. Access your **Snyk account settings** to retrieve your **API token for authentication** and record it in your notepad.

   ![](images/70.png)

1. On your lab computer, open the Azure DevOps portal with the **eShopOnWeb** project in a web browser. Click on the **marketplace icon > Browse Marketplace**.

   ![](images/61.png)

1. Search for **Snyk Security Scan** in the Marketplace and open it.

   ![](images/73.png)

1. On the **Microsoft Security DevOps** page, click on **Get it for free**.

   ![](images/74.png)

1. Select the desired Azure DevOps organization and **Install**.

   ![](images/64.png)

1. Click **Proceed to organization** once the installation is complete.

   ![](images/65.png)

1. Go to the Azure portal, search for **Entra ID (1)**, and select **Microsoft Entra ID (2)**.

   ![](images/78.png)

1. Scroll down and select **App registrations** under **manage**, then select the app registration named **odluser<inject key="DeploymentID" enableCopy="false"/>-Defender_for_cloud-suffix** under all application.

   ![](images/77.png)

1. Copy the name of the app registration and paste it into your notepad.

   ![](images/80.png)

1. Navigate back to Azure DevOps and click on **Project Settings** at the bottom left of the **eShopOnWeb** project.

1. Scroll down on the **Project Settings** page and select **Service Principal.**

   ![](images/75.png)

1. Click on **New Service Principal.**

   ![](images/92.png)

1. Search for and select **Snyk Authentication**, then click **Next**.

   ![](images/76.png)

1. Paste the **API** under **Snyk API Token (1)** and the **App registration** name under **Service connection name (2)** that you noted earlier. Then, click **Save (3)**.

   ![](images/79.png)

1. You should see that a new service principal has been created.

### 2. Integrate Snyk in Azure DevOps Pipeline

1. Navigate to the **Pipelines** pane in the **Pipelines** hub.

1. Click **New pipeline** in the **Pipeline** window.

1. Select **Azure Repos Git (YAML)** on the **Where is your code?** pane.

   ![](images/10.png)

1. Click **eShopOnWeb** on the **Select a repository** pane.

   ![](images/12.png)

1. Scroll down and select **Starter pipeline** on the **Configure your pipeline** pane.

   ![](images/164.png)

1. Define your build pipeline in a file named `azure-pipelines.yml` with the following content:

   ```yaml
   trigger:
   - master

   pool:
    vmImage: 'ubuntu-latest'

   steps:
   - task: MicrosoftSecurityDevOps@1
     displayName: 'Microsoft Security DevOps'
     inputs:
       sarifOutputFile: '$(Build.ArtifactStagingDirectory)/results.sarif' # Ensures SARIF output location

   - script: |
      echo "Verifying SARIF file creation..."
      if [ -f "$(Build.ArtifactStagingDirectory)/results.sarif" ]; then
         echo "SARIF file created successfully."
         ls -la $(Build.ArtifactStagingDirectory)
      else
         echo "SARIF file not found."
         exit 1
      fi
   displayName: 'Verify SARIF File Creation'

   - task: PublishBuildArtifacts@1
   displayName: 'Publish Artifact: CodeAnalysisLogs'
   inputs:
      PathtoPublish: '$(Build.ArtifactStagingDirectory)/results.sarif'
      ArtifactName: 'CodeAnalysisLogs'
   ```
1. Click on the 12th line then press enter.

1. Expand **Task** pane from the right side then search and select **Snyk Security Scan**

   ![](images/82.png)

1. Under **Snyk Security Scan**, complete the following steps:

   - For **Snyk API token**, select the API from the dropdown menu.
   - For **What do you want to test**, choose **Code**.
   - For **Code Testing severity threshold**, enter **High**.
   - Fail build if snyk finds issues should be **disable**.

      ![](images/83.png)

   - For project name in Snyk, enter **Run Snyk Security Scan**.
   - For Organization name (or ID) in snyk, enter the **organization id you copied** in snyk account.
   - For Additional command-line arge for Snyk CLI (advanced), enter **--sarif --sarif-file-output=$(Build.ArtifactStagingDirectory)/results.sarif**.
   - Click **Add**.

     ![](images/lab5-18.png)
  
       - This section specifies that the pipeline should be triggered whenever a commit is made to the `main` branch.

1. Click **Save and Run** then again click **Save and Run** to start the Build Pipeline process.

   ![](images/84.png)

1. You are prompted with **Permissions Needed**, as well as an orange bar saying :

    ```
    This pipeline needs permission to access a resource before this run can continue
    ```
1. Click on **View**.

   ![](images/86.png)

1. From the **Waiting for Review** pane, click **Permit**.

   ![](images/87.png)

1. Validate the message in the **Permit popup** window, and confirm by clicking **Permit**.

   ![](images/88.png)

1. Wait for the build pipeline to complete. After running the pipeline, review the results of the Snyk scan in the pipeline logs. Snyk will report any vulnerabilities found.

   ![](images/85.png)

1. Go to the Summary tab, and you will find the **Snyk report** under the *Snyk tab*.

   ![](images/93.png)

1. Go to the Azure portal and search for **Microsoft Defender for Cloud**.

1. In the Microsoft Defender for Cloud dashboard, navigate to the **Recommendations** section.

## **Task 8: Role of Defender Cloud Security Posture Management (DCSPM)** 

DevOps Security Posture, as part of Cloud Security Posture Management (CSPM), involves assessing and enhancing the security of your development and deployment processes within the cloud. Here's a detailed discussion on what CX (Customer Experience) gets in terms of DevOps security posture within CSPM:

### 1. Overview of CSPM and DevOps Security

CSPM focuses on ensuring that your cloud environment adheres to security best practices and compliance requirements. It helps in identifying misconfigurations, vulnerabilities, and compliance issues within your cloud resources. When integrated with DevOps processes, CSPM aims to secure the entire development lifecycle from code to deployment.

### 2. Key Aspects of DevOps Security Posture in CSPM

- **Infrastructure as Code (IaC) Scanning**: CSPM solutions often include the ability to scan IaC templates for security vulnerabilities and misconfigurations before they are deployed. This ensures that any security issues are identified early in the development process, reducing the risk of deploying insecure configurations.

- **Continuous Integration/Continuous Deployment (CI/CD) Pipelines Security**: CSPM tools can monitor and secure CI/CD pipelines to ensure that the processes involved in building, testing, and deploying applications are secure. This includes checking for vulnerabilities in the build environment, ensuring secure access controls, and monitoring the integrity of the deployment process.

- **Compliance Monitoring**: CSPM solutions can help ensure that your DevOps processes comply with industry standards and regulations. This includes monitoring for adherence to policies like GDPR, HIPAA, or PCI-DSS within your development and deployment practices.

- **Threat Detection and Response**: Advanced CSPM tools provide real-time threat detection and response capabilities. They can identify suspicious activities or potential security incidents in your DevOps processes and provide recommendations for remediation.

- **Configuration Management**: Ensuring that configurations used in development, staging, and production environments are securely managed and adhere to best practices is a critical aspect of DevOps security posture. CSPM tools help in monitoring and enforcing secure configurations across all environments.

### 3. Demonstrating DevOps Security Posture

To showcase the DevOps security posture provided by CSPM, you can create a lab or demonstration that highlights the following:

- **IaC Scanning**: Show how CSPM tools can scan Terraform or other IaC templates for security issues. Demonstrate how vulnerabilities are identified and how they can be fixed before deployment.

- **Pipeline Security**: Set up a demo of a CI/CD pipeline integrated with CSPM tools. Highlight how the tool monitors the pipeline for security issues, such as insecure dependencies or misconfigured pipeline settings.

- **Compliance Checks**: Display how the CSPM solution continuously monitors for compliance with various standards and regulations, providing actionable insights and recommendations.

- **Incident Response**: Simulate a security incident within the DevOps process and demonstrate how the CSPM tool detects and responds to the threat, including the remediation steps.

1. Integrating CSPM with DevOps processes provides a comprehensive approach to securing your development lifecycle, from coding to deployment. By leveraging CSPM tools, you ensure that security is baked into the DevOps process, reducing vulnerabilities and ensuring compliance.

1. Navigate back to the **Recommendations** page of defender for cloud, filter the recommendations by selecting only **Azure DevOps connections**, and then choose the **Azure DevOps repositories should have secrets scanning findings resolved** recommendation.

   ![alert_detected](images/124.png)

1. Review the **Take actions** tab, where you'll find steps to remediate the issue.

   ![alert_detected](images/126.png)

1. Navigate to the **Findings** tab, where you'll find detailed information related to the identified issues.

   ![alert_detected](images/125.png)

1. Navigate back to the **Recommendations** page, filter the recommendations by selecting only **Azure DevOps connections**, and then choose the **Azure DevOps repositories should have infrastructure as code scanning findings resolved** recommendation.

   ![alert_detected](images/127.png)

1. Review the **Take actions** tab, where you'll find steps to remediate the issue.

   ![alert_detected](images/128.png)

1. Navigate to the **Findings** tab to review detailed information about the identified issues. To resolve the errors in the `sql.tf` file, review the file in Azure DevOps and address the issue at line 31. Similarly, you can check the other IDs listed and fix the corresponding errors.

   ![alert_detected](images/129.png)

1. Navigate back to the **Recommendations** page, filter the recommendations by selecting only **Azure DevOps connections**, and then choose the **Azure DevOps repositories should have dependency vulnerability scanning findings resolved** recommendation.

   ![alert_detected](images/144.png)

1. Review the **Take actions** tab, where you'll find steps to remediate the issue.

   ![alert_detected](images/142.png)

1. Navigate to the **Findings** tab, where you'll find detailed information related to the identified issues.

   ![alert_detected](images/143.png)

1. Navigate back to the **Recommendations** page, filter the recommendations by selecting only **Azure DevOps connections**, and then choose the **Azure DevOps repositories should require minimum two-reviewer approval for code pushes** recommendation.

   ![alert_detected](images/148.png)

1. Review the **Take actions** tab, where you'll find steps to remediate the issue.

   ![alert_detected](images/145.png)

1. Click on the **View recommendations for all resources** from the top.

   ![alert_detected](images/146.png)

1. Expand the affected resources and review the unhealthy resources.

   ![alert_detected](images/147.png)

 >**Note**: It may take up to 24 hours to receive all the recommendations.

#### **Remove the Azure DevOps billing**

1. On the lab computer, switch to the browser window displaying Azure DevOps organization homepage and select **Organization Settings** at bottom left corner.

2. Under **Organization Settings** select **Billing** and click on **Change billing** button to open Change billing pane.

3. In the **Change billing** pane, select **Remove billing** setting and click on Save.

## **Summary**  
In this module, you explored various aspects of securing CI/CD pipelines in Azure DevOps. You began by understanding the role of CI/CD pipelines and identifying potential security vulnerabilities within them. Using Defender for DevOps, you pinpointed and assessed security issues, learning how they could impact your pipeline. You also reviewed GitHub Advanced Security (GHAS) and Defender for DevOps, gaining insight into their features and pricing. Integration tasks included securing your pipeline with GHAS and Defender for DevOps, and connecting Azure DevOps to Microsoft Defender for Cloud (MDC) Finally, you examined the role of Defender Cloud Security Posture Management (DCSPM) in enhancing your cloud security posture. This module provided a comprehensive approach to securing DevOps environments and pipelines, integrating various security tools to improve overall security and management.

## You have successfully completed the lab
