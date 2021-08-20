# react-and-spring-data-rest

The included Azure DevOps pipeline provision the entire infrastructure needed to run the application, both in Azure Container Instances and Azure App Services for Linux Containers, using a provided ARM Template.

It is assumed that you have an already deployed Azure Container Registry **(with an [admin user enabled](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-authentication?tabs=azure-cli#admin-account))** in a separate Resource Group at the same subscription in which you wish to deploy. If you have no instance of ACR available in your subscription, [you should create one first](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal).

## Importing into Azure DevOps

1. Create a new project in your organization with default settings

2. In the project settings, create the following two Service Connections:

   1.  An **[Azure Resource Manager](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)** Service Connection to the Azure Subscription you intend to use. ***Do not scope the service connection to a Resource Group***. 
   2. An [**Docker Registry**](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#docker-hub-or-others) Service Connection configured to the existing Azure Container Registry in the same subscription you intend to use.

3. **[Import this repository](https://docs.microsoft.com/en-us/azure/devops/repos/git/import-git-repository?view=azure-devops)** into Azure Repos

4. In Azure Pipelines, create a new pipeline from the imported repository, selecting "Existing Azure Pipelines YAML file" as the template, choosing the "[/pipeline/pipeline.yml](pipeline/pipeline.yml)" YAML contained into the imported repository. Don't run the pipeline yet, just save it.

5. Edit the newly created pipeline, and hit the "Variables" button, and define the following variables, adjusting the values to your environment:

   | Variable Name               | Description                                                  | Example Value              |
   | --------------------------- | ------------------------------------------------------------ | -------------------------- |
   | containerRegistry           | Name of the ACR Service Connection in Azure DevOps           | ACR Service Connection     |
   | acrName                     | Name of the ACR in Azure (without .azurecr.io)               | my-acr                     |
   | acrResourceGroupName        | Name of the Resource Group in which resides the ACR instance in your Azure subscription | RG-CONTAINER-REGISTRY      |
   | azureSubscriptionConnection | Name of the Azure Resource Manager Service Connection in Azure DevOps | ARM Service Connection     |
   | subscriptionId              | ID of the Azure Subscription                                 | *an unique identifier*     |
   | appName                     | Name of the app. Also used to provide the names of the resources in Azure | react-and-spring-data-rest |
   | location                    | Azure Region to deploy the resources                         | North Europe               |
   | sqlServerPassword           | Password for the Azure SQL instance (must be marked as a secret) | P@$$w0rd                   |

6. Run your pipeline. Notice the flag **"Clean up infrastructure?"** in the "Run pipeline" panel. Leave it unchecked in the first run to allow for the pipeline to run the "Deploy" job, which creates or updates the infrastructure. After your tests, to remove all the resources created, check the flag to allow the "Cleanup" job to run, which removes the resource group of the application.
   Please note that the ACR and the container images created will not be removed, so you must remove them manually.

