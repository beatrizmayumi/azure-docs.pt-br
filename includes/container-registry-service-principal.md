---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032361"
---
## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para criar uma entidade de serviço com acesso ao registro de contêiner, execute o script a seguir no [Azure Cloud Shell](../articles/cloud-shell/overview.md) ou uma instalação local da [CLI do Azure](/cli/azure/install-azure-cli). O script é formatado para o shell do Bash.

Antes de executar o script, atualize a variável `ACR_NAME` com o nome do registro de contêiner. O valor `SERVICE_PRINCIPAL_NAME` deve ser exclusivo dentro do seu locatário do Azure Active Directory. Se você receber um erro "`'http://acr-service-principal' already exists.`", especifique um nome diferente para a entidade de serviço.

Opcionalmente, você pode modificar `--role` o valor no comando [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] se quiser conceder permissões diferentes. Para obter uma lista completa de funções, consulte [Funções e permissões do ACR](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Depois de executar o script, anote a **ID** e a **senha** da entidade de serviço. Quando você tiver suas credenciais, poderá configurar os aplicativos e serviços para se autenticarem no registro de contêiner como a entidade de serviço.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Usar uma entidade de serviço existente

Para conceder acesso de registro a uma entidade de serviço existente, você precisa atribuir uma nova função à entidade de serviço. Assim como ocorre ao criar uma nova entidade de serviço, é possível conceder acesso de proprietário, pull, push e pull, dentre outros.

O script a seguir usa o comando [AZ role Assignment Create][az-role-assignment-create] para conceder permissões de *pull* a uma entidade de serviço que `SERVICE_PRINCIPAL_ID` você especifica na variável. Ajuste o valor `--role` se você desejar conceder um nível diferente de acesso.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
