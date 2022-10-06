# AKS-Articles
Code base related to the AKS + Azure DevOps Medium articles
https://nicolas-yuen.medium.com/deploying-aks-with-terraform-on-azure-devops-65c7ef95d737
Github: https://github.com/rgjtokiota/Terraform_AKS.git

extensión az agregar --name aks-preview
registro de características az -n MultiAgentpoolPreview --namespace Microsoft.ContainerService
registro de características az -n VMSSPreview --namespace Microsoft.ContainerService

#Luego actualice su registro del proveedor de recursos de AKS:
registro de proveedor az -n Microsoft.ContainerService

## Creación del grupo de recursos 
az group create -n AksTerraform-RG -l eastus2 

## Creando la cuenta de almacenamiento 
az storage account create -n <YOUR_STORAGE_ACCOUNT_NAME> -g AksTerraform-RG -l eastus2
az storage account create -n rgjstorageaks -g AksTerraform-RG -l eastus2

## Creating a tfstate container 
az storage container create -n tfstate --account-name <YOUR_STORAGE_ACCOUNT_NAME>
az storage container create -n tfstate --account-name rgjstorageaks
## Creación de KeyVault 
az keyvault create -n <YOUR_KV_NAME> -g AksTerraform-RG -l eastus2
az keyvault create -n rgjkvaks -g AksTerraform-RG -l eastus2


## Creación de un token SAS para la cuenta de almacenamiento, almacenamiento en el contenedor de almacenamiento KeyVault 
az storage container generate-sas --account-name <YOUR_STORAGE_ACCOUNT_NAME> --expiry 2020-01-01 --name tfstate --permissions dlrw -o json | xargs az keyvault secret set --vault-name <YOUR_KV_NAME> --name TerraformSASToken --value

az storage container generate-sas --account-name rgjstorageaks --expiry 2023-01-01 --name tfstate --permissions dlrw -o json | xargs az keyvault secret set --vault-name rgjkvaks --name TerraformSASToken --value 



## creación de una entidad de servicio para AKS y Azure DevOps (ERROR)
az ad sp create-for-rbac -n "AksTerraformSPN"
## crear una clave ssh si aún no tiene una 
ssh-keygen -f ~/.ssh/id_rsa_terraform
## Almacene la clave pública en Azure KeyVault 
az keyvault secret set --vault-name <SU_NOMBRE_KV> --name LinuxSSHPubKey -f ~/.ssh/id_rsa_terraform.pub > /dev/null
az keyvault secret set --vault-name rgjkvaks --name LinuxSSHPubKey -f ~/.ssh/id_rsa_terraform.pub > /dev/null
## Almacene la identificación principal del servicio en Azure KeyVault 
az keyvault secret set --vault-name <SU_NOMBRE_KV> --name spn-id --value <SPN_ID> /dev/null
az keyvault secret set --vault-name rgjkvaks --name spn-id --value <SPN_ID> /dev/null
## Almacenar el secreto principal del servicio en Azure KeyVault 
az keyvault secret set --vault-name <SU_NOMBRE_KV> --name spn-secret --value <SPN_SECRET> /dev/null