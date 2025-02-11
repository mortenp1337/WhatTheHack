# What The Hack: DevOps with GitHub 

## Challenge 3 - Infrastructure as Code (IaC)

[< Previous](challenge02.md) - [Home](../readme.md) - [Next >](challenge04.md)

### Introduction

Now that we have some code, we need an environment to deploy it to! The term Infrastructure as Code (IaC) refers to using templates (code) to repeatedly and consistently create the dev, test, prod (infrastructure) environments. We can automate the process of deploying the Azure services we need with an Azure Resource Manager (ARM) template. 

Review the following articles:

- [Azure Resource Manager overview](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)
- [What is Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview?tabs=bicep)


The following image illustrates the areas covered in this challenge

  ![Challenge overview](img/challenge-3-overview.png)

### Challenge

We will use GitHub Actions to automate the deployment of our Azure infrastructure. For our application, we will deploy 3 environments: `dev`, `test` and `prod`. Each environment will have its own Web App, however all of our environments will share a single Resource Group, App Service Plan, Application Insights instance, Log Analytics Workspace and Azure Container Registry. 

> **Note**<br> 
> in real deployments, you will likely not share all of these resources.
>

1. Review the Bicep templates. The `main` Bicep file creates the resource groups and reference a module Bicep file (`container-webapp-template`) that defines a number of parameters and uses them to create the Resource Group, App Service Plan, Web App, Application Insights, Log Analytics Workspace and Azure Container Registry. 

2. Update the parameters section of the `main` Bicep template, replacing default `prefix` parameter value with a unique lowercase 5 letter name. The resulting name needs to be globally unique to correctly provision resources. Notice the `webAppName` parameter on line #6 - you will override this placeholder value later when you call the Bicep template.

3. Create a GitHub workflow (`deploy.yml`) that runs manually (*not* triggered by a push or pull request).

4. Configure your workflow to accomplish the following:

    - Use a service principal to authenticate to Azure
    - Use the "Deploy Azure Resource Manager (ARM) Template" action to call your Bicep template in your repo. 
    
> **Note**<br> 
> The `main` Bicep template must be deployed with subscription scope ([hint](https://github.com/Azure/arm-deploy#inputs)).  
>


5. Manually run your workflow. When your workflow completes successfully, go to the Azure portal to see the `dev` environment. 

If everything worked, we are going to call the Bicep template again, but override the `webAppName` parameter in the Bicep template.

6. Create an environment variable called `targetEnv` in your workflow and set the *value* to the `webAppName` in your Bicep template, BUT, replace the "dev" with "test" (i.e., 'myapp-devops-test').

7. Update your "Deploy Azure Resource Manager (ARM) Template" action to call your Bicep template in your repo and override the `webAppName` paramater with the new `targetEnv` environment variable.

8. Rerun the workflow. When your workflow completes successfully, go to the Azure portal to see the new `test` App Service. 

9. If everything worked, replace the "test" in your `targetEnv` with "prod" and rerun the workflow. When your workflow completes successfully, go to the Azure portal to see the new `prod` App Service. 

You should see all three environments in Azure.

### Success Criteria

- Your `deploy.yaml` workflow completes without any errors and overrides the `webAppName` parameter when calling the ARM template.
- Your resource group contains 7 resources: 3 App Services (dev, test, prod), 1 Application Insights, 1 Log Analytics workspace, 1 App Service plan and 1 Container registry. 

### Learning Resources

- [What is Infrastructure as Code?](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-infrastructure-as-code)
- [Introduction to GitHub Actions](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions)
- [Manually trigger a workflow](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch)
- [Deploy Bicep files by using GitHub Actions](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/deploy-github-actions?tabs=userlevel%2CCLI) 
- [GitHub Action for Azure Resource Manager (ARM) deployment](https://github.com/Azure/arm-deploy)
- [Deploy Azure Resource Manager templates by using GitHub Actions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-github-actions)
- [Parameters in Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-cli#parameters)


### Advanced Challenges (optional)

Instead of changing the `targetEnv` variable for each environment that we want to create in the deploy.yaml, you can configure the workflow to prompt the user to enter the environment name before the workflow runs - eliminating the need to hard code the environment name.
- Delete the `targetEnv` environment variable you created earlier.
- Configure your workflow to collect the environment name as a [workflow input](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onworkflow_callinputs) and use that value to override the `webAppName` parameter when calling the ARM template.

> **Note**<br>
> If you are interested in learning more about Infrastructure as Code, there are [multiple](https://github.com/microsoft/WhatTheHack) What the Hacks that cover it in greater depth.
>

[< Previous](challenge02.md) - [Home](../readme.md) - [Next >](challenge04.md)