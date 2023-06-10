# Play.Infra

Play Economy Infrastructure components

## Add the GitHub package source

```powershell
$owner="PlayEcomony-Microservices"
$gh_pat="[PAT HERE]"
dotnet nuget add source --username USERNAME --password $gh_pat --store-password-in-clear-text --name github "https://nuget.pkg.github.com/$owner/index.json"
```

## Creating the Azure resource group

```powershell
$appname="playeconomy"
az group create --name $appname --location eastus
```

## Create the Cosmos DB account

```powershell
az cosmosdb create --name $appname --resource-group $appname --kind MongoDB --enable-free-tier
```

## Creating the Service Bus namespace

```powershell
az servicebus namespace create --name $appname --resource-group $appname --sku Standard
```

## Creating Azure Container Registry

```powershell
az acr create --name $appname --resource-group $appname --sku Basic
```

## Creating the AKS cluster

```powershell
$acrName="playeconomybkm"
az extension add --name aks-preview
az feature register --name "EnableWorkloadIdentityPreview" --namespace "Microsoft.ContainerService"

# Wait for this command to reach a "Registered" State
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableWorkloadIdentityPreview')].{Name:name, State:properties.state}"

az provider register --namespace Microsoft.ContainerService
az aks create -n $appname -g $appname --node-vm-size Standard_B2s --node-count 2 --attach-acr $acrName --enable-oidc-issuer --enable-workload-identity --generate-ssh-keys

az aks get-credentials --name $appname --resource-group $appname
```
