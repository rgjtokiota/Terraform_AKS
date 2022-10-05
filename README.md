# AKS-Articles
Code base related to the AKS + Azure DevOps Medium articles

Github: https://github.com/rgjtokiota/Terraform_AKS.git

extensión az agregar --name aks-preview
registro de características az -n MultiAgentpoolPreview --namespace Microsoft.ContainerService
registro de características az -n VMSSPreview --namespace Microsoft.ContainerService

#Luego actualice su registro del proveedor de recursos de AKS:
registro de proveedor az -n Microsoft.ContainerService

## Creación del grupo de recursos 
az group create -n AksTerraform-RG -l eastus2 

## Creando la cuenta de almacenamiento 
az storage account create -n storageaks -g AksTerraform-RG -l eastus2
## Creating a tfstate container 
az storage container create -n tfstate --account-name aks-state
## Creación de KeyVault 
az keyvault create -n kv_aks -g AksTerraform-RG -l eastus2
 Nombre de ubicación Grupo de recursos 
---------- -------------- --- ------------ 
eastus2 <SU_NOMBRE_KV> AksTerraform-RG
## Creación de un token SAS para la cuenta de almacenamiento, almacenamiento en el contenedor de almacenamiento KeyVault 
az xargs az keyvault secret set --vault-name <SU_NOMBRE_KV> --name TerraformSASToken --value
## creación de una entidad de servicio para AKS y Azure DevOps 
az ad sp create-for-rbac -n "AksTerraformSPN"
## crear una clave ssh si aún no tiene una 
ssh-keygen -f ~/.ssh/id_rsa_terraform
## Almacene la clave pública en Azure KeyVault 
az keyvault secret set --vault-name <SU_NOMBRE_KV> --name LinuxSSHPubKey -f ~/.ssh/id_rsa_terraform.pub > /dev/null
## Almacene la identificación principal del servicio en Azure KeyVault 
az keyvault secret set --vault-name <SU_NOMBRE_KV> --name spn-id --value <SPN_ID> /dev/null
## Almacenar el secreto principal del servicio en Azure KeyVault 
az keyvault secret set --vault-name <SU_NOMBRE_KV> --name spn-secret --value <SPN_SECRET> /dev/null