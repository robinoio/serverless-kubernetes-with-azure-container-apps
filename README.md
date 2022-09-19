# Serverless Kubernetes with Azure Container Apps

Does Azure Container Apps deliver on the promise of serverless microservices reducing management, responsibility, and
complexity? Let's find out!

This repository is part of the
[Serverless Kubernetes with Azure Container Apps](https://go.robino.io/serverless-kubernetes-with-azure-container-apps "Serverless Kubernetes with Azure Container Apps")
blog post.

## Deploying the app

**Step 1:** Start your preferred terminal and connect to Azure using the Azure CLI and your identity (user account).

```bash
az login
```

**Step 2:** List the available subscriptions and locate the preferred subscription identifier.

```bash
az account list --output table
```

**Step 3:** Set the subscription that you're using to deploy the resources. Use the subscription identifier from the
previous step.

```bash
az account set --subscription <subscriptionId>
```

**Step 4:** Because the Azure Container Apps extension is still in preview, you'll need to install the `containerapp` extension.

```bash
az extension add \
  --name containerapp \
  --upgrade
```

**Step 5:** Register the `Microsoft.App` and `Microsoft.OperationalInsights` namespaces.

```bash
az provider register --namespace Microsoft.App
az provider register --namespace Microsoft.OperationalInsights
```

**Step 6:** Verify the status of the namespaces.

```bash
az provider show \
  --namespace Microsoft.App \
  --output table

az provider show \
  --namespace Microsoft.OperationalInsights \
  --output table
```

**Step 7:** Set the environment variables used throughout the upcoming commands.

```bash
RESOURCE_GROUP=apps-rg
LOCATION=westeurope
WORKSPACE=robino-apps-log01
ENVIRONMENT=robino-apps-env01
APP=robino-apps-app01
```

**Step 8:** Create the `apps-rg` resource group.

```bash
az group create \
  --location $LOCATION \
  --name $RESOURCE_GROUP
```

**Step 9:** Create the `robino-apps-env01` container apps environment. The apps deployed in this environment use
the same Log Analytics workspace created simultaneously with the environment.

```bash
az containerapp env create \
  --name $ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION
```

**Step 10:** Deploy the `robino-apps-app01` container app to the environment using the latest `containerapps-helloworld`
container image.

```bash
az containerapp create \
  --name $APP \
  --resource-group $RESOURCE_GROUP \
  --environment $ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress external
```

**Step 11:** Verify the deployment by copying and using the URL in your browser. The container app will open.

**Step 12:** Remove the `apps-rg` resource group, including all resources. It'll prompt you for confirmation.

```bash
az group delete --name $RESOURCE_GROUP
```

**Step 13:** Finally, log out of the Azure CLI.

```bash
az logout
```
