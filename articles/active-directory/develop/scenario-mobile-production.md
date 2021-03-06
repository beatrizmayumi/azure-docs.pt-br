---
title: Mover aplicativos móveis chamando APIs da Web para produção-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo móvel que chama APIs da Web (mover para produção)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702021"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplicativo móvel que chama APIs da Web – mover para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a confiabilidade do seu aplicativo antes de movê-lo para produção.

## <a name="handling-errors-in-mobile-applications"></a>Tratamento de erros em aplicativos móveis

Várias condições de erro podem ocorrer em seu aplicativo neste ponto. Os principais cenários a serem tratados são falhas silenciosas e fallbacks para a interação. Outras condições que você deve considerar para produção incluem situações sem rede, interrupções de serviço, requisitos de consentimento do administrador e outros casos específicos do cenário.

Cada biblioteca MSAL possui código de exemplo e conteúdo wiki que descreve como lidar com essas condições:

- [Wiki do MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki do MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Mitigar e investigar problemas

Para diagnosticar problemas em seu aplicativo, ele ajuda a coletar dados. Para obter informações sobre os tipos de dados que você pode coletar, consulte os wikis da plataforma MSAL.

- Os usuários podem pedir ajuda quando encontrarem problemas. Uma prática recomendada é capturar e armazenar logs temporariamente e fornecer um local onde os usuários possam carregá-los. O MSAL fornece extensões de log para capturar informações detalhadas sobre a autenticação.
- Se estiver disponível, habilite a telemetria por meio do MSAL para coletar dados sobre como os usuários estão entrando em seu aplicativo.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Experimente amostras adicionais disponíveis em [exemplos | Aplicativos cliente públicos de desktop e móveis](sample-v2-code.md#desktop-and-mobile-public-client-apps)
