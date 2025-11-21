#!/bin/bash
set -euo pipefail

###
### Variáveis
###
grupoRecursos=timewise-rg
# Altere a Região conforme orientação do Professor
regiao=eastus
# Outras opções recomendadas:
# brazilsouth | eastus2 | westus | westus2

# Altere para seu RM
rm=rm555276
nomeACR="acr$rm"
skuACR=Basic

###
### Criação do Grupo de Recursos
###
# Verifica a existência do grupo de recursos
if [ $(az group exists --name $grupoRecursos) = true ]; then
    echo "O grupo de recursos $grupoRecursos já existe"
else
    az group create --name $grupoRecursos --location $regiao
    echo "Grupo de recursos $grupoRecursos criado na localização $regiao"
fi

###
### Criação do Azure Container Registry
###
if az acr show --name $nomeACR --resource-group $grupoRecursos &> /dev/null; then
    echo "O ACR $nomeACR já existe"
else
    az acr create --resource-group $grupoRecursos --name $nomeACR --sku $skuACR
    echo "ACR $nomeACR criado com sucesso"

    az acr update --name $nomeACR --resource-group $grupoRecursos --admin-enabled true
    echo "Habilitado com sucesso o usuário Administrador para o ACR $nomeACR"
fi

###
### Exibir credenciais do Admin do ACR (somente para testes e aprendizado)
###
ADMIN_USER=$(az acr credential show --name $nomeACR --query "username" -o tsv)
ADMIN_PASSWORD=$(az acr credential show --name $nomeACR --query "passwords[0].value" -o tsv)

export ACR_ADMIN_USER=$ADMIN_USER
export ACR_ADMIN_PASSWORD=$ADMIN_PASSWORD

echo "Usuário Admin do ACR: $ACR_ADMIN_USER"
echo "Senha Admin do ACR: $ACR_ADMIN_PASSWORD"
