---
title: O que há de novo na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece informações sobre novas versões e recursos para os serviços cognitivas do Azure Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 6fa7d6a93a56cc531df238a8580207ef7a89d5d0
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732613"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

O API de Análise de Texto é atualizado em uma base contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece informações sobre novas versões e recursos.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Visualização pública de reconhecimento de entidade nomeada v3 – outubro de 2019

A próxima versão do NER (reconhecimento de entidade nomeada) agora está disponível para visualização pública e fornece detecção expandida e categorização de entidades encontradas no texto. Ela oferece:

* Reconhecimento dos seguintes novos tipos de entidade:
    * Telefone
    * Endereço IP

* Um [novo ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) para reconhecer tipos de entidade de informações pessoais (somente em inglês)
* Separe pontos de extremidade para [reconhecimento de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e [vinculação de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

A vinculação de entidades dá suporte a inglês e espanhol. O suporte ao idioma NER varia de acordo com o tipo de entidade. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o reconhecimento de entidade nomeada v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Visualização pública do Análise de Sentimento v3-outubro de 2019

A [próxima versão do análise de sentimento](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) agora está disponível para visualização pública e fornece melhorias significativas na precisão e nos detalhes da categorização e da Pontuação de texto da API. Além disso, ele fornece:

* Rotulagem automática para diferentes sentimentos no texto.
* Análise de sentimentos e saída em um nível de documento e frase. 

Ele dá suporte a Inglês (`en`), japonês (`ja`), chinês simplificado (`zh-Hans`), chinês tradicional (`zh-Hant`), francês (`fr`), italiano (`it`), espanhol (`es`), holandês (`nl`), Português (`pt`) e alemão (`de`) e está disponível nas seguintes regiões: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`e `West US 2`. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o Análise de Sentimento v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Próximos passos

* [O que é a API de Análise de Texto?](overview.md)  
* [Cenários de usuário de exemplo](text-analytics-user-scenarios.md)
* [Análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Detecção de idioma](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidade](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md)
