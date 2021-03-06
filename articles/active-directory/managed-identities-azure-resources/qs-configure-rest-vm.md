---
title: Configurar identidades gerenciadas na VM do Azure usando REST-Azure AD
description: Instruções passo a passo para configurar um sistema e identidades gerenciadas atribuídas pelo usuário em uma VM do Azure usando CURL para fazer chamadas da API REST.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429997"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando chamadas da API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade do sistema gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em uma VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se você usa o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [SO de distribuição do Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [instale o console local da CLI do Azure](/cli/azure/install-azure-cli).
- Se você estiver usando o console local do Azure CLI, faça logon no Azure usando `az login` com uma conta associada à assinatura do Azure que deseja gerenciar as identidades gerenciadas pelo sistema ou pelo usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure usando CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída ao sistema habilitada, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Criar um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#az-group-create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie um [adaptador de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para a VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma VM usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria uma VM denominada *myVM* com uma identidade gerenciada designada pelo sistema, conforme identificada no corpo da solicitação pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos da solicitação**
   
   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 
   
   **Corpo da solicitação**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Ativar identidade atribuída pelo sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída ao sistema em uma VM que foi originalmente provisionada sem ela, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Use o seguinte comando CURL para chamar o terminal REST do Azure Resource Manager para ativar a identidade gerenciada atribuída pelo sistema em sua VM, conforme identificado no corpo da solicitação pelo valor `{"identity":{"type":"SystemAssigned"}` para uma VM denominada *myVM*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário que esteja atribuída à VM, é necessário listar as identidades gerenciadas atribuídas pelo usuário usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se você tiver identidades gerenciadas atribuídas pelo usuário e atribuídas à VM, conforme identificado no valor `identity` na resposta, pule para a etapa 3, que mostra como manter as identidades gerenciadas atribuídas pelo usuário ao ativar a identidade gerenciada atribuída pelo sistema na sua VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 
   
   **Corpo da solicitação**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM com identidades gerenciadas atribuídas pelo usuário existentes, você precisa adicionar `SystemAssigned`ao`type` valor.  
   
   Por exemplo, se sua VM tiver as identidades gerenciadas atribuídas pelo usuário `ID1` e `ID2` atribuídas a ela e você quiser adicionar a identidade gerenciada atribuída pelo sistema à VM, use a seguinte chamada CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` pelos valores apropriados para seu ambiente.

   A versão da API `2018-06-01` armazena identidades gerenciadas designadas pelo usuário no valor `userAssignedIdentities` em um formato de dicionário, em oposição ao valor `identityIds` em um formato de matriz usado na versão da API `2017-12-01`.
   
   **API DE 2018 VERSÃO-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída ao sistema em uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize a VM usando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para desabilitar a identidade gerenciada atribuída pelo sistema.  O exemplo a seguir desativa a identidade gerenciada designada pelo sistema conforme identificada no corpo da solicitação pelo valor `{"identity":{"type":"None"}}` de uma VM denominada *myVM*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário que esteja atribuída à VM, é necessário listar as identidades gerenciadas atribuídas pelo usuário usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se você tiver identidades gerenciadas atribuídas pelo usuário e atribuídas à VM, conforme identificado no valor `identity` na resposta, vá para a etapa 3, que mostra como manter as identidades gerenciadas atribuídas pelo usuário e desabilitar a identidade gerenciada atribuída pelo sistema na sua VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerenciada atribuída pelo sistema de uma máquina virtual que tenha identidades gerenciadas designadas pelo usuário, remova `SystemAssigned` do valor `{"identity":{"type:" "}}` e mantenha o valor `UserAssigned` e os valores do dicionário `userAssignedIdentities` se você estiver usando **Versão da API 2018-06-01**. Se você estiver usando a **versão da API 2017-12-01** ou anterior, mantenha o array `identityIds`.

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover a identidade gerenciada atribuída pelo usuário em uma VM do Azure usando CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie um [adaptador de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para a VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui: [Crie uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie uma VM usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria uma VM denominada *myVM* no grupo de recursos *myResourceGroup* com uma identidade gerenciada designada pelo usuário `ID1`, conforme identificado no corpo da solicitação pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
 
   **API DE 2018 VERSÃO-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **VERSÃO DA API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada usuário atribuído a uma VM existente do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui, [Crie uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que você não exclua as identidades gerenciadas atribuídas pelo usuário ou pelo sistema atribuídas à VM, é necessário listar os tipos de identidade atribuídos à VM usando o seguinte comando CURL. Se houver identidades gerenciadas atribuídas ao conjunto de dimensionamento de máquinas virtuais, elas serão listadas no valor `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.

    Se você tiver qualquer usuário ou identidades gerenciadas atribuídas pelo sistema atribuídas à VM, conforme identificado no valor de `identity` na resposta, pule para a etapa 5 que mostra como manter a identidade gerenciada atribuída pelo sistema ao adicionar uma identidade gerenciada atribuída pelo usuário em sua VM.

4. Se você não tiver nenhuma identidade gerenciada atribuída pelo usuário atribuída à sua VM, use o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para atribuir a primeira identidade gerenciada atribuída pelo usuário à VM.

   Os exemplos a seguir designam uma identidade gerenciada designada pelo usuário, `ID1` para uma VM denominada *myVM* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   **API DE 2018 VERSÃO-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |
 
   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Se você tiver uma identidade gerenciada designada pelo usuário ou designada pelo sistema atribuída à sua VM:
   
   **API DE 2018 VERSÃO-06-01**

   Adicionar a identidade atribuída pelo usuário gerenciada para o `userAssignedIdentities` valor do dicionário.
    
   Por exemplo, se você tiver uma identidade gerenciada atribuída pelo sistema e a identidade gerenciada atribuída pelo usuário `ID1` atualmente atribuída à sua VM e quiser adicionar a identidade gerenciada atribuída pelo usuário `ID2` a ela:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   Reter as identidades gerenciadas atribuídas pelo usuário que você gostaria de manter no valor da matriz `identityIds` ao adicionar a nova identidade gerenciada atribuída pelo usuário.

   Por exemplo, se você tiver uma identidade gerenciada atribuída pelo sistema e a identidade gerenciada atribuída pelo usuário `ID1` atualmente atribuída à sua VM e quiser adicionar a identidade gerenciada atribuída pelo usuário `ID2` a ela: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída ao usuário a uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

1. Recupere um token de acesso do portador, que você usará na próxima etapa no cabeçalho Autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário que gostaria de manter atribuída à VM ou remover a identidade gerenciada atribuída pelo sistema, é necessário listar as identidades gerenciadas usando o seguinte comando CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.
 
   Se houver identidades gerenciadas atribuídas à VM, elas serão listadas na resposta no valor `identity`.

   Por exemplo, se você tiver identidades gerenciadas atribuídas pelo usuário `ID1` e `ID2` atribuídas à sua VM, e só quiser manter `ID1` atribuída e manter a identidade atribuída pelo sistema:
   
   **API DE 2018 VERSÃO-06-01**

   Adicionar `null` para o usuário atribuído gerenciado identidade que você deseja remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   Reter apenas as identidades gerenciadas atribuídas pelo usuário que você gostaria de manter na matriz `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos da solicitação**

   |Cabeçalho da solicitação  |Description  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se sua VM tiver identidades gerenciadas atribuídas pelo sistema e atribuídas pelo usuário, você poderá remover todas as identidades gerenciadas atribuídas pelo usuário alternando para usar somente a identidade gerenciada atribuída pelo sistema usando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos da solicitação**

|Cabeçalho da solicitação  |Description  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se sua VM tiver somente identidades gerenciadas atribuídas pelo usuário e você quiser removê-las, use o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos da solicitação**

|Cabeçalho da solicitação  |Description  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.| 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como criar, listar ou excluir identidades gerenciadas atribuídas pelo usuário usando o REST, consulte:

- [Criar, listar ou excluir identidades gerenciadas atribuídas pelo usuário usando chamadas da API REST](how-to-manage-ua-identity-rest.md)
