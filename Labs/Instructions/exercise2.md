# Exercise1: Deploying Resources with Azure Developer CLI

### Estimated Duration: 30 minutes

## Lab Scenario

In this exercise, you will deploy the necessary resources for your FastAPI application using the Azure Developer CLI (azd). You will set up your environment, initialize and configure the deployment process, and use azd commands to provision and deploy services in Azure. 

## Lab Objectives

After you complete this exercise, you will:

- Learn to provision and deploy application resources in Azure using the Azure Developer CLI (azd).

### Task1: Understanding Azure Developer CLI and the Deployment Workflow

In this task, you will gain an understanding of the Azure Developer CLI (azd) and how it facilitates the deployment of cloud resources. You'll explore the main.bicep file, the core infrastructure template for your application, and learn about the primary resources it defines, including key parameters, dependencies, and outputs. By the end, you'll be familiar with how azd uses this file to automate provisioning, setting the foundation for deploying resources with ease in the next steps.

>**Labtip:** **AZD (Azure Developer CLI)** is a command-line tool designed to streamline the process of building, deploying, and managing Azure applications. It simplifies the interaction with Azure resources by allowing developers to define infrastructure using code, deploy applications, and manage environments in a more efficient and automated way.

1. From the desktop, open **Visual Studio Code**.

   ![](../media/ex1img0.png)

1. On **Visual Studio Code** pane, select **Open Folder** under **file** menu from top menu.

    ![](../media/ex1img6.png)

1. Navigate to `C:\contoso\contoso-creative-writer` directory, click on **Select folder**.

1. Once you open the folder in Visual Studio Code, on Do you trust the authors of the files of this folder? pop up, click on Yes, I trust the authors.

   ![](../media/ex1img7.png)

1. Once you have the **contoso-creative-writer** directory opened, ensure you have the source code files from the **explorer pane**

1. From the explorer menu, navigate to `/infra/main.bicep` file to review. A Bicep file is a simplified, readable syntax for defining and deploying Azure resources, which is compiled into ARM templates for deployment.

1. In the `main.bicep`. navigate to **ai** module definition.
 
   ```bicep
    module ai 'core/host/ai-environment.bicep' = {
    name: 'ai'
    scope: resourceGroup
    params: {
        location: location
        tags: tags
        hubName: !empty(aiHubName) ? aiHubName : 'ai-hub-${resourceToken}'
        projectName: !empty(aiProjectName) ? aiProjectName : 'ai-project-${resourceToken}'
        keyVaultName: !empty(keyVaultName) ? keyVaultName : '${abbrs.keyVaultVaults}${resourceToken}'
        storageAccountName: !empty(storageAccountName)
        ? storageAccountName
        : '${abbrs.storageStorageAccounts}${resourceToken}'
        openAiName: !empty(openAiName) ? openAiName : 'aoai-${resourceToken}'
        openAiConnectionName: !empty(openAiConnectionName) ? openAiConnectionName : 'aoai-connection'
        openAiContentSafetyConnectionName: !empty(openAiContentSafetyConnectionName) ? openAiContentSafetyConnectionName : 'aoai-content-safety-connection'
        openAiModelDeployments: array(contains(aiConfig, 'deployments') ? aiConfig.deployments : [])
        logAnalyticsName: !useApplicationInsights
        ? ''
        : !empty(logAnalyticsWorkspaceName)
            ? logAnalyticsWorkspaceName
            : '${abbrs.operationalInsightsWorkspaces}${resourceToken}'
        applicationInsightsName: !useApplicationInsights
        ? ''
        : !empty(applicationInsightsName) ? applicationInsightsName : '${abbrs.insightsComponents}${resourceToken}'
        containerRegistryName: !useContainerRegistry
        ? ''
        : !empty(containerRegistryName) ? containerRegistryName : '${abbrs.containerRegistryRegistries}${resourceToken}'
        searchServiceName: !useSearch ? '' : !empty(searchServiceName) ? searchServiceName : '${abbrs.searchSearchServices}${resourceToken}'
        searchConnectionName: !useSearch ? '' : !empty(searchConnectionName) ? searchConnectionName : 'search-service-connection'
      }
    }
   ```

   >This module sets up essential AI infrastructure, such as Azure OpenAI, Key Vault, storage accounts, and log analytics.

   >It allows customization via parameters like openAiName, keyVaultName, and storageAccountName.

   >Additionally, it handles the setup for application insights and container registries.

1. The next part defines the configuration for deploying a Bing Search resource in Azure. It is set to be deployed globally and provides the search functionality for the application.

   ```bicep
   module bing 'core/bing/bing-search.bicep' = {
   name: 'bing'
   scope: resourceGroup
   params: {
      name: 'agent-bing-search'
      location: 'global'
      }
    }
   ```

   >This module configures a Bing Search resource in the global region, which will be used for search-related operations.

   >The name and location parameters can be customized to define the Bing service's characteristics.

1. The next part provisions a container app environment, which includes setting up containerized applications in Azure. It connects the container environment to a container registry and log analytics workspace for monitoring.

   ```bicep
    module containerApps 'core/host/container-apps.bicep' = {
    name: 'container-apps'
    scope: resourceGroup
    params: {
        name: 'app'
        location: location
        tags: tags
        containerAppsEnvironmentName: 'agent-ca-env'
        containerRegistryName: ai.outputs.containerRegistryName
        logAnalyticsWorkspaceName: ai.outputs.logAnalyticsWorkspaceName
    }
    }
   ```

   >This module defines the container apps environment where containerized applications will be deployed. It connects to the container registry and log analytics workspace, both of which are configured earlier in the AI module.

1. The next part is the core of the `API` part of the application. The API app is used to serve backend services.

   ```bicep
    module apiContainerApp 'app/api.bicep' = {
    name: 'api'
    scope: resourceGroup
    params: {
        name: 'agent-api'
        location: location
        tags: tags
        identityName: managedIdentity.outputs.managedIdentityName
        identityId: managedIdentity.outputs.managedIdentityClientId
        containerAppsEnvironmentName: containerApps.outputs.environmentName
        containerRegistryName: containerApps.outputs.registryName
        openAi_35_turbo_DeploymentName: !empty(openAi_35_turbo_DeploymentName) ? openAi_35_turbo_DeploymentName : 'gpt-35-turbo'
        openAi_4_DeploymentName: !empty(openAi_4_DeploymentName) ? openAi_4_DeploymentName : 'gpt-4'
        openAi_4_eval_DeploymentName: !empty(openAi_4_eval_DeploymentName) ? openAi_4_eval_DeploymentName : 'gpt-4-evals'
        openAiEmbeddingDeploymentName: openAiEmbeddingDeploymentName
        openAiEndpoint: ai.outputs.openAiEndpoint
        openAiName: ai.outputs.openAiName
        openAiType: openAiType
        openAiApiVersion: openAiApiVersion
        aiSearchEndpoint: ai.outputs.searchServiceEndpoint
        aiSearchIndexName: aiSearchIndexName
        appinsights_Connectionstring: ai.outputs.applicationInsightsConnectionString
        bingApiEndpoint: bing.outputs.endpoint
        bingApiKey: bing.outputs.bingApiKey
    }
    }
   ```

   >The apiContainerApp module sets up the backend API containerized application, including identity and OpenAI model deployments.

1. The last part will provision the `web` part of the application which server frontend interfaces.

   ```bicep
    module webContainerApp 'app/web.bicep' = {
    name: 'web'
    scope: resourceGroup
    params: {
        name: 'agent-web'
        location: location
        tags: tags
        identityName: managedIdentity.outputs.managedIdentityName
        identityId: managedIdentity.outputs.managedIdentityClientId
        containerAppsEnvironmentName: containerApps.outputs.environmentName
        containerRegistryName: containerApps.outputs.registryName
        apiEndpoint: apiContainerApp.outputs.SERVICE_ACA_URI
    }
    }
   ```

   >The webContainerApp module configures the frontend web application, which connects to the API container. These modules ensure that the API and web applications are deployed within the container environment, leveraging services like OpenAI and Bing.

### Task2: Streamlining Azure Resource Deployment with azd

In this task, you will be using the Azure Developer CLI (azd) to deploy the resources defined in your Bicep templates to Azure. The Azure Developer CLI simplifies the process of managing infrastructure as code, allowing you to efficiently deploy, manage, and monitor your applications and resources directly from the command line.

1. As you are on **Visual Studio Code** pane, open **New terminal** from **Terminal** menu using the top menu bar.

   ![](../media/ex1img1.png)

1. Once the terminal is open, select **v (1)** from the right corner and select **Git Bash (2)** from the menu.

   ![](../media/ex1img2.png)

   >**LabTip: Git Bash** is a command-line tool for Windows that lets users run Git commands and Unix-like shell commands.

1. Once the **GitBash** terminal opened, run the following command to Sign in and authenticate the azd tool.

   ```bash
   azd auth login
   ```
1. Once you run this command, a sign in page will be opened in your browser. Please use these credentials to sign in.

   - **Username:** <inject key="AzureAdUserEmail"></inject> and click on **Next**.

      ![](../media/gs-06.png)

   - **Password:** <inject key="AzureAdUserPassword"></inject> and click on **Sign in**.

      ![](../media/gs-07.png)

1. Once you logged in successfully, navigate back to your **GitBash** terminal and run the following command to authenticate **Azure CLI** tool aswell.

   ```bash
   az login
   ```
   >Note: You may need to minimize Visual Studio Code pane to see the pop up window to sign in.

1. Once you are on the pop up window, select **Work or school account** and click on **Continue**.

   ![](../media/ex1img3.png)

1. In the sign in page, provide the following:

   Username: <inject key="AzureAdUserEmail"></inject> and click on **Next**.

   ![](../media/ex1img4.png)

   Password: <inject key="AzureAdUserPassword"></inject> and click on **Sign in**.

   ![](../media/ex1img5.png)

1. When prompts, click on **No, sign in to this app only** and continue.

1. Return to your **Visual Studio Code** terminal, now it prompts you to select subscription with a list of subscriptions, enter **1** and hit enter.

1. Once you have successfully logged in, run the following command which will deploy all the defined resources in Azure.

   ```
   azd up
   ```

   >This may take upto 15 minutes to deploy all the rsources, till then please move to next exercise as that is a read-only exercise where you will get to know the core application and technology stacks used.

## Summary

In this exercise, you have learned how to use the Azure Developer CLI (AZD) to deploy resources for your application. You explored how to define and configure infrastructure using a Bicep file, initialized tracing for monitoring, and successfully deployed resources such as container apps and AI environments using AZD.





