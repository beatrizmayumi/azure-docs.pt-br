---
title: Suporte a TLS do Hub IoT do Azure
description: Práticas recomendadas no uso de conexões TLS seguras para dispositivos e serviços que se comunicam com o Hub IoT
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722883"
---
# <a name="tls-support-in-iot-hub"></a>Suporte a TLS no Hub IoT

O Hub IoT usa o protocolo TLS para proteger conexões de dispositivos e serviços de IoT. Atualmente, há suporte para três versões do protocolo TLS, principalmente as versões 1,0, 1,1 e 1,2.

O TLS 1,0 e o 1,1 são considerados herdados e são planejados para substituição. Para obter mais informações, consulte [preterindo TLS 1,0 e 1,1 para o Hub IOT](iot-hub-tls-deprecating-1-0-and-1-1.md). É altamente recomendável que você use o TLS 1,2 como a versão de TLS preferencial ao se conectar ao Hub IoT.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Restringir conexões ao TLS 1,2 em seu recurso de Hub IoT

Para maior segurança, é aconselhável configurar seus hubs IoT para permitir *somente* conexões de cliente que usam TLS versão 1,2 e para impor o uso de [codificações recomendadas](#recommended-ciphers).

Para essa finalidade, provisione um novo hub IoT em qualquer uma das [regiões com suporte](#supported-regions) e defina a propriedade `minTlsVersion` como `1.2` na especificação de recurso do Hub IOT do modelo de Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

O recurso do Hub IoT criado usando essa configuração recusará clientes de dispositivo e serviço que tentam se conectar usando as versões 1,0 e 1,1 do TLS. Da mesma forma, o handshake TLS será recusado se a mensagem de SAUDAção do cliente não listar nenhuma das [codificações recomendadas](#recommended-ciphers).

> [!NOTE]
> A propriedade `minTlsVersion` é somente leitura e não pode ser alterada depois que o recurso do Hub IoT é criado. Portanto, é essencial que você teste e valide corretamente que *todos* os seus dispositivos e serviços de IOT sejam compatíveis com o TLS 1,2 e as [codificações recomendadas](#recommended-ciphers) com antecedência.

### <a name="supported-regions"></a>Regiões com suporte

Os hubs IoT que exigem o uso de TLS 1,2 podem ser criados nas seguintes regiões:

* Leste dos EUA
* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2

> [!NOTE]
> Após os failovers, a propriedade `minTlsVersion` do Hub IoT permanecerá efetiva na região emparelhada após o failover.

### <a name="recommended-ciphers"></a>Codificações recomendadas

Os hubs IoT configurados para aceitar somente o TLS 1,2 também impedirão o uso das seguintes codificações recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usar o TLS 1,2 em seus SDKs do Hub IoT

Use os links abaixo para configurar o TLS 1,2 e as codificações permitidas nos SDKs do cliente do Hub IoT.

| Linguagem | Compatível com TLS 1,2 | Documentação |
|----------|-------------------|---------------|
| C        | Sim               | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Sim               | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Sim               | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Sim               | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Sim               | [Link](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Usar o TLS 1,2 na instalação do IoT Edge

IoT Edge dispositivos podem ser configurados para usar o TLS 1,2 ao se comunicar com o Hub IoT. Para essa finalidade, use a [página de documentação IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).