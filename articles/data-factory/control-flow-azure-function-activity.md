---
title: Atividade do Azure Functions no Azure Data Factory
description: Saiba como usar a atividade de função do Azure para executar uma função do Azure em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 781c5a579fa0cd0383e95b79df1f81f74008111c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679970"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade de função do Azure no Azure Data Factory

Saiba como usar a atividade do [Azure Functions](../azure-functions/functions-overview.md) para executar uma função do Azure em um pipeline do Data Factory. Para executar uma função do Azure, você precisará criar uma conexão de serviço vinculado e uma atividade que especifica a função do Azure que você planeja executar.

Para ver uma introdução de oito minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço de função vinculado do Azure

O tipo de retorno da função do Azure deve ser um `JObject` válido. (Tenha em mente que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é um `JObject`.) Qualquer tipo de retorno diferente de `JObject` falha e gera o conteúdo de resposta de erro do usuário *não é um JObject válido*.

| **Propriedade** | **Descrição** | **Necessária** |
| --- | --- | --- |
| type   | A propriedade Type deve ser definida como: **AzureFunction** | sim |
| url do aplicativo de função | URL para o Aplicativo de Funções do Azure. O formato é `https://<accountname>.azurewebsites.net`. Essa URL é o valor na seção **URL** ao exibir o Aplicativo de funções no portal do Azure  | sim |
| teclas de função | Tecla de acesso para o Azure Function. Clique na seção **Gerenciar** seção para a função respectiva e copie-a na **Tecla de Função** ou na **Tecla Host**. Saiba mais aqui: [Azure Functions gatilhos e associações http](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade do Azure Function

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Necessária** |
| --- | --- | --- | --- |
| name  | Nome da atividade no pipeline  | String | sim |
| type  | O tipo de atividade é 'AzureFunctionActivity' | String | sim |
| serviço vinculado | O serviço de função do Azure vinculado para o Aplicativo de funções correspondente do Azure  | Referência de serviço vinculado | sim |
| nome da função  | Nome da função no Aplicativo de funções do Azure que essa atividade chama | String | sim |
| method  | Método da API REST para a chamada de função | Tipos de cadeia de caracteres com suporte: "GET", "POST", "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "cabeçalhos": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não |
| corpo  | corpo que é enviado junto com a solicitação para o método de api de função  | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) ou objeto.   | Necessário para os métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga de solicitação na seção  [Esquema de carga de solicitação](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Roteamento e consultas

A atividade do Azure Function dá suporte a **roteamento**. Por exemplo, se a função do Azure tiver o ponto de extremidade `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, o `functionName` a ser usado na atividade do Azure function será `<functionName>/<value>`. Você pode parametrizar essa função para fornecer as `functionName` desejadas em tempo de execução.

A atividade do Azure Function também dá suporte a **consultas**. Uma consulta deve ser incluída como parte do `functionName`. Por exemplo, quando o nome da função é `HttpTriggerCSharp` e a consulta que você deseja incluir é `name=hello`, você pode construir o `functionName` na atividade de função do Azure como `HttpTriggerCSharp?name=hello`. Essa função pode ser parametrizada para que o valor possa ser determinado em tempo de execução.

## <a name="timeout-and-long-running-functions"></a>Funções de tempo limite e execução longa

Azure Functions o tempo limite após 230 segundos, independentemente da configuração de `functionTimeout` que você definiu nas configurações. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar esse comportamento, siga um padrão assíncrono ou use Durable Functions. A vantagem do Durable Functions é que eles oferecem seu próprio mecanismo de controle de estado, portanto, você não precisará implementar o seu.

Saiba mais sobre Durable Functions neste [artigo](../azure-functions/durable/durable-functions-overview.md). Você pode configurar uma atividade do Azure function para chamar a função durável, que retornará uma resposta com um URI diferente, como [Este exemplo](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Como `statusQueryGetUri` retorna o status HTTP 202 enquanto a função está em execução, você pode sondar o status da função usando uma atividade da Web. Basta configurar uma atividade da Web com o campo `url` definido como `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Quando a função durável for concluída, a saída da função será a saída da atividade da Web.


## <a name="sample"></a>Amostra

Você pode encontrar um exemplo de um Data Factory que usa uma função do Azure para extrair o conteúdo de um arquivo tar [aqui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as atividades no Data Factory no [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md).
