# Deploy Azure Databricks with the Azure CLI
- Step 1: Sign in
    - Sign in using the az login command if you’re using a local install of the CLI.
    - Follow the steps displayed in your terminal to complete the authentication process.
    - 
    ```
    az login
    ```
- Step 2: Install the Azure CLI extension
    - When working with extension references for the Azure CLI, you must first install the extension. Azure CLI extensions give you access to experimental and pre-release commands that have not yet shipped as part of the core CLI. To learn more about extensions including updating and uninstalling, see Use extensions with Azure CLI.
    - Install the extension for databricks by running the following command:
    - 
    ```
    az extension add --name databricks
    ```
- Step 3: Create a resource group
    - Azure Databricks, like all Azure resources, must be deployed into a resource group. Resource groups allow you to organize and manage related Azure resources.
    - For this quickstart, create a resource group named _ databricks-quickstart _ in the westus2 location with the following az group create command:
    - 
    ```
    az group create --name databricks-quickstart --location westus2
    ```
- Step 4: Create an Azure Databricks workspace
    - Use the az databricks workspace create create an Azure Databricks workspace.
    - 
    ```
    az databricks workspace create
    --resource-group databricks-quickstart \
    --name mydatabricksws  \
    --location westus  \
    --sku standard
    ```
- Step 5: review the deployed resource
    - You can either use the Azure portal to check the Azure Databricks workspace or use the following Azure CLI or Azure PowerShell script to list the resource.
    - 
    ```
    echo "Enter your Azure Databricks workspace name:" &&
    read databricksWorkspaceName &&
    echo "Enter the resource group where the Azure Databricks workspace exists:" &&
    read resourcegroupName &&
    az databricks workspace show -g $resourcegroupName -n $databricksWorkspaceName
    ```
- Step 6: review the deployed resources
    - 
    ```
    az resource list --resource-group exampleRG
    ```