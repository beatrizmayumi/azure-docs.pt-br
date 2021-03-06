---
title: Mostrar direções de rota em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a exibir as instruções entre dois locais em um mapa usando o SDK da Web do Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 359f2b42ca6f56087be53a5aeb328fe43a478d63
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988270"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar direções de A para B

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa.

Há duas maneiras de fazer isso. A primeira maneira é a consultar a [API de Rotas do Azure Mapas](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é usar a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a API de [rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). As duas formas são discutidas abaixo.

## <a name="query-the-route-via-service-module"></a>A rota por meio do módulo de serviço de consulta

<iframe height='500' scrolling='no' title='Mostrar direções de A para B em um mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Mostrar rotas de A para B em um mapa (Módulo de Serviço)</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele passa o `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `routeURL` representa uma URL para as operações de [rota](https://docs.microsoft.com/rest/api/maps/route) do Azure Mapas.

O terceiro bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O quarto bloco de código cria objetos Start e [end points](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e os adiciona ao objeto DataSource.

Uma linha é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) para LineString. Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linhas em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa textos ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Os textos ou os ícones são renderizados como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de código consulta o serviço de roteamento do Azure Maps, que faz parte do [módulo de serviço](how-to-use-services-module.md). O método [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) do RouteUrl é usado para obter uma rota entre os pontos inicial e final. Uma coleção de recursos geojson da resposta é extraída usando o método `geojson.getFeatures()` e é adicionada à fonte de fontes. Em seguida, renderiza a resposta como uma rota no mapa. Para mais informações sobre como adicionar uma linha ao mapa, consulte [adicionar uma linha no mapa](map-add-line-layer.md).

O último bloco de código define os limites do mapa usando a propriedade do mapa [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

A consulta de rota, a fonte de dados, o símbolo, as camadas de linha e os limites da câmera são criados e definidos dentro do [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mapa. Essa estrutura de código garante que os resultados sejam exibidos somente depois que o mapa for totalmente carregado.

## <a name="query-the-route-via-fetch-api"></a>Consultar a rota por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar instruções de A para B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar direções de A para B em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O terceiro bloco de código cria os pontos de início e destino para a rota e os adiciona à fonte de dados. Você pode consultar [adicionar um pin no mapa](map-add-pin.md) para obter instruções sobre como usar [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha em [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Veja as propriedades de uma camada de símbolo em [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

O próximo bloco de código cria pontos `SouthWest` e `NorthEast` a partir dos pontos inicial e de destino e define os limites do mapa usando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O último bloco de código usa a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a [API de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Em seguida, a resposta é analisada. Se a resposta foi bem-sucedida, as informações de latitude e longitude são usadas para criar uma matriz uma linha conectando esses pontos. Os dados de linha são então adicionados à fonte de dados para renderizar a rota no mapa. Você pode consultar [adicionar uma linha no mapa](map-add-line-layer.md) para obter instruções.

A consulta de rota, a fonte de dados, o símbolo, as camadas de linha e os limites da câmera são criados e definidos dentro do [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mapa. Novamente, queremos garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com o mapa – eventos do mouse](./map-events.md)
