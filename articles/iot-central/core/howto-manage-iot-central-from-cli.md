---
title: Gerenciar IoT Central de CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seu aplicativo IoT Central usando a CLI. Você pode exibir, modificar e remover o aplicativo usando a CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8526eb50faf300892c66ac186eac25adecf62231
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019019"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerenciar IoT Central de CLI do Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar IoT Central aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar [CLI do Azure](/cli/azure/) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se preferir executar CLI do Azure em seu computador local, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli). Ao executar CLI do Azure localmente, use o comando **AZ login** para entrar no Azure antes de tentar os comandos neste artigo.

## <a name="create-an-application"></a>Criar um aplicativo

Use o comando [AZ iotcentral app Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) para criar um aplicativo IOT central em sua assinatura do Azure. Por exemplo:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Esses comandos primeiro criam um grupo de recursos na região leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o comando **AZ iotcentral app Create** :

| Parâmetro         | Description |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém o aplicativo. Esse grupo de recursos já precisa existir na sua assinatura. |
| local          | Por padrão, esse comando usa o local do grupo de recursos. No momento, você pode criar um aplicativo IoT Central nas regiões **leste dos EUA**, **oeste dos EUA**, **Europa setentrional**ou **Europa Ocidental** ou na **Austrália** ou **Pacífico Asiático** geografia. |
| name              | Digite o nome do aplicativo no portal do Azure. |
| subdomínio         | O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é https://mysubdomain.azureiotcentral.com. |
| sku               | No momento, você pode usar **ST1** ou **ST2**. Confira [Preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | O modelo de aplicativo a usar. Para obter mais informações, confira a tabela a seguir: |
| nome de exibição      | O nome do aplicativo, conforme exibido na interface do usuário. |

**Modelos de aplicativo**

| Nome do modelo            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos.
| iotc-pnp-preview@1.0.0   | Cria um aplicativo Plug and Play (versão prévia) vazio para você preencher com seus próprios dispositivos e modelos de dispositivo. |
| iotc-condition@1.0.0     | Cria um aplicativo com um modelo de monitoramento de condição em uma análise na loja. Use este modelo para conectar e monitorar o ambiente de armazenamento. |
| iotc-consumption@1.0.0   | Cria um aplicativo com o modelo de monitoramento de consumo de água. Use este modelo para monitorar e controlar o fluxo de água. |
| iotc-distribution@1.0.0  | Cria um aplicativo com um modelo de distribuição digital. Use este modelo para melhorar a eficiência da saída do depósito ao digitalização de ativos e ações de chave. |
| iotc-inventory@1.0.0     | Cria um aplicativo com um modelo de gerenciamento de inventário inteligente. Use este modelo para automatizar o recebimento, movimentação de produtos, contagem de ciclos e acompanhamento de sensores. |
| iotc-logistics@1.0.0     | Cria um aplicativo com um modelo de logística conectado. Use este modelo para acompanhar sua remessa em tempo real entre ar, água e terreno com monitoramento de local e condição. |
| iotc-meter@1.0.0         | Cria um aplicativo com o modelo de monitoramento do medidor inteligente. Use este modelo para monitorar o consumo de energia, o status da rede e identificar as tendências para melhorar o atendimento ao cliente e o gerenciamento de medidor inteligente.  |
| iotc-patient@1.0.0       | Cria um aplicativo com o modelo de monitoramento contínuo do paciente. Use este modelo para estender o atendimento ao paciente, renovações e gerenciar doenças. |
| iotc-power@1.0.0         | Cria um aplicativo com o modelo de monitoramento de painel solar. Use este modelo para monitorar o status do painel solar, as tendências de geração de energia. |
| iotc-quality@1.0.0       | Cria um aplicativo com o modelo de monitoramento de qualidade de água. Use este modelo para monitorar digitalmente a qualidade da água.|
| iotc-store@1.0.0         | Cria um aplicativo com um modelo de check-out de análise no repositório. Use este modelo para monitorar e gerenciar o fluxo de check-out dentro de seu repositório. |
| iotc-waste@1.0.0         | Cria um aplicativo com um modelo de gerenciamento de resíduos conectado. Use este modelo para monitorar os operadores de lixo e de campo de expedição. |

## <a name="view-your-applications"></a>Exibir seus aplicativos

Use o comando [AZ iotcentral app List](/cli/azure/iotcentral/app#az-iotcentral-app-list) para listar seus aplicativos IOT central e exibir metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o comando [AZ iotcentral app Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) para atualizar os metadados de um aplicativo IOT central. Por exemplo, para alterar o nome de exibição do aplicativo:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover um aplicativo

Use o comando [AZ iotcentral app Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) para excluir um aplicativo IOT central. Por exemplo:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central do CLI do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)
