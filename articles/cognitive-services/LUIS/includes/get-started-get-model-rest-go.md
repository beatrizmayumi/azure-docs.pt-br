---
title: Obter o modelo com chamadas REST em Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: ec61abca19579426818e227687e08e66b73969cb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505848"
---
## <a name="prerequisites"></a>Pré-requisitos

* Chave inicial.
* Importe o aplicativo [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório GitHub cognitive-services-language-understanding.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. A ID do aplicativo é mostrada no painel do aplicativo.
* O ID da versão no aplicativo que recebe os enunciados. A ID padrão é “0.1”.
* Linguagem de programação [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Alterar o modelo de maneira programática

Use Go para adicionar uma [API](https://aka.ms/luis-apim-v3-authoring) de entidade aprendida pelo computador ao aplicativo. 

1. Crie um arquivo chamado `predict.go`. Adicione os códigos a seguir:
    
    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )
    
    // main function
    func main() {
    
        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"
    
        // NOTE: change to your starter key
        var authoringKey = "YOUR-KEY"
    
        // NOTE: change to your starter key's endpoint, for example, westus.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"  
    
        var version = "0.1"
    
        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `
    
        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)
    
        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)
    
        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }
    
    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)
    
        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){
    
        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)
    
        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){
    
        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){
    
        client := &http.Client{}
    
        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)
    
        fmt.Println("body")
        fmt.Println(body)
    
        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }    
    ```

1. Substitua os valores a seguir:

    * `YOUR-KEY` com a chave inicial
    * `YOUR-ENDPOINT` com o ponto de extremidade, por exemplo, `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` com o ID do aplicativo

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, insira o seguinte comando para compilar o arquivo Go:

    ```console
    go build model.go
    ```  

1. Execute o aplicativo GO da linha de comando inserindo o texto a seguir no prompt de comando: 

    ```console
    go run model.go
    ```

## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)