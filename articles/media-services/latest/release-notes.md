---
title: Notas de versão dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 12/13/2019
ms.author: juliako
ms.openlocfilehash: 52d8dda8b543e5bdf3ca88ae3784df65be3a2ba1
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962936"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure v3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` em seu leitor de RSS feed.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-sdk), CLI ou um dos SDKs suportados.

Para obter mais informações, consulte [Guia de migração para migrar do Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="january-2020"></a>Janeiro de 2020

### <a name="improvements-in-media-processors"></a>Melhorias nos processadores de mídia

- Suporte aprimorado para fontes entrelaçadas na análise de vídeo – esse conteúdo agora é desentrelaçado corretamente antes de ser enviado aos mecanismos de inferência.
- Ao gerar miniaturas com o modo "melhor", o codificador agora pesquisa além de 30 segundos para selecionar um quadro que não seja monodesvio.
 
## <a name="november-2019"></a>Novembro de 2019

### <a name="live-transcription-preview"></a>Visualização de transcrição ao vivo

A transcrição ao vivo agora está em visualização pública e disponível para uso na região oeste dos EUA 2.

A transcrição ao vivo foi projetada para trabalhar em conjunto com eventos ao vivo como um recurso complementar.  Há suporte para eventos ao vivo de codificação de passagem e Standard ou Premium.  Quando esse recurso é habilitado, o serviço usa o recurso de [conversão de fala em texto](../../cognitive-services/speech-service/speech-to-text.md) de serviços cognitivas para transcrever as palavras faladas no áudio de entrada em texto. Esse texto é disponibilizado para entrega junto com vídeo e áudio em protocolos MPEG-DASH e HLS. A cobrança é baseada em um novo medidor de complemento que é custo adicional para o evento ao vivo quando ele está no estado "em execução".  Para obter detalhes sobre a transcrição dinâmica e a cobrança, consulte [transcrição ao vivo](live-transcription.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como um recurso de visualização na região oeste dos EUA 2. Ele dá suporte à transcrição de palavras faladas em inglês (en-US) somente no momento.

### <a name="content-protection"></a>Proteção de conteúdo

O recurso de *prevenção de reprodução de token* lançado em regiões limitadas de volta em setembro agora está disponível em todas as regiões.
Os clientes dos serviços de mídia agora podem definir um limite no número de vezes que o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [prevenção de reprodução de token](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Novos parceiros de codificador dinâmico recomendados

Adição de suporte para os seguintes novos codificadores de parceiros recomendados para streaming ao vivo RTMP:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 e máximo de câmeras de ação](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Aprimoramentos de codificação de arquivo

- Uma nova predefinição de codificação com reconhecimento de conteúdo está disponível agora. Ele produz um conjunto de MP4s alinhado a GOP usando a codificação com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço executa uma análise leve inicial do conteúdo de entrada. Ele usa esses resultados para determinar o número ideal de camadas, a taxa de bits apropriada e as configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de baixa complexidade e de complexidade média, em que os arquivos de saída têm taxas de bits menores, mas com uma qualidade que ainda oferece uma boa experiência aos visualizadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte as [especificações de API aberta](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Melhor desempenho e multithreading para o redimensionador em Media Encoder Standard. Em condições específicas, o cliente deve ver um aumento de desempenho entre 5-40% de codificação VOD. O conteúdo de baixa complexidade codificado em várias taxas de bits verá o maior desempenho aumenta. 
- A codificação padrão agora mantém uma cadência GOP regular para conteúdo de VFR (taxa de quadros variável) durante a codificação VOD ao usar a configuração de GOP baseada em tempo.  Isso significa que o cliente que envia conteúdo de taxa de quadros misto que varia entre 15-30 fps, por exemplo, agora deve ver as distâncias GOP regulares calculadas na saída para arquivos MP4 de streaming de taxa de bits adaptável. Isso melhorará a capacidade de alternar diretamente entre as faixas ao entregar HLS ou DASH. 
-  Sincronização antivírus aprimorada para conteúdo de origem de taxa de quadros variável (VFR)

### <a name="video-indexer-video-analytics"></a>Video Indexer, análise de vídeo

- Os quadros-chave extraídos usando a predefinição VideoAnalyzer agora estão na resolução original do vídeo em vez de serem redimensionados. A extração de quadro-chave de alta resolução fornece imagens de qualidade original e permite que você use os modelos de inteligência artificial baseados em imagem fornecidos pelo Microsoft Pesquisa Visual Computacional e Visão Personalizada Services para obter ainda mais informações do seu vídeo.

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Mídia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear dinâmica de eventos ao vivo

Os serviços de mídia v3 estão anunciando a visualização de 24 horas x 365 dias de codificação linear dinâmica de eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Mídia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição da *Azure Media indexer* e da versão *prévia do Azure Media indexer 2*. Para as datas de desativação, consulte este tópico de [componentes herdados](../previous/legacy-components.md) . Os [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui esses processadores de mídia herdados.

Para obter mais informações, consulte [migrar do Azure Media indexer e Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Mídia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>O par regional da África do Sul está aberto para os serviços de mídia 

Os serviços de mídia agora estão disponíveis na África do Sul e nas regiões do oeste da África do Sul.

Para obter mais informações, consulte [nuvens e regiões nas quais os serviços de mídia v3 existem](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Serviços de Mídia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a reprovação dos processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame), que estão sendo desativados. Para as datas de desativação, consulte este tópico de [componentes herdados](../previous/legacy-components.md) .

Para obter detalhes, consulte [migrar WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [migrar ame para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir conteúdo protegido com restrição de token, os usuários finais precisam obter um token que é enviado como parte da solicitação de entrega de chave. O recurso de *prevenção de reprodução de token* permite que os clientes dos serviços de mídia definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [prevenção de reprodução de token](content-protection-overview.md#token-replay-prevention).

A partir de julho, o recurso de visualização só estava disponível em Centro dos EUA e centro-oeste dos EUA.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Subcorte de vídeo

Agora você pode cortar ou subcortar um vídeo ao codificá-lo usando um [trabalho](https://docs.microsoft.com/rest/api/media/jobs). 

Essa funcionalidade funciona com qualquer [transformação](https://docs.microsoft.com/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) . 

Veja exemplos:

* [Subclipe um vídeo com o .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte Azure Monitor para métricas e logs de diagnóstico dos serviços de mídia

Agora você pode usar Azure Monitor para exibir dados de telemetria emitidos pelos serviços de mídia.

* Use os logs de diagnóstico Azure Monitor para monitorar solicitações enviadas pelo ponto de extremidade de entrega de chave dos serviços de mídia. 
* Monitore as métricas emitidas pelos [pontos de extremidade de streaming](streaming-endpoint-concept.md)dos serviços de mídia.   

Para obter detalhes, consulte [monitorar as métricas dos serviços de mídia e os logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte a faixas de vários áudio no empacotamento dinâmico 

Ao transmitir ativos que têm várias faixas de áudio com vários codecs e linguagens, o [empacotamento dinâmico](dynamic-packaging-overview.md) agora dá suporte a faixas de vários áudio para a saída HLS (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>O par regional da Coreia está aberto para os serviços de mídia 

Os serviços de mídia agora estão disponíveis nas regiões da Coreia central e da Coreia do Sul. 

Para obter mais informações, consulte [nuvens e regiões nas quais os serviços de mídia v3 existem](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Melhorias de desempenho

Atualizações adicionadas que incluem melhorias de desempenho dos serviços de mídia.

* O tamanho máximo de arquivo com suporte para processamento foi atualizado. Confira, [cotas e limitações](limits-quotas-constraints.md).
* A [codificação acelera as melhorias](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador interno.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado às predefinições do codificador interno. Para obter mais informações, consulte [codificação com reconhecimento de conteúdo](content-aware-encoding.md). 

## <a name="march-2019"></a>Março de 2019

O empacotamento dinâmico agora dá suporte a Dolby Atmos. Para obter mais informações, consulte [codecs de áudio com suporte pelo empacotamento dinâmico](dynamic-packaging-overview.md#audio-codecs).

Agora você pode especificar uma lista de ativos ou filtros de conta, que se aplicariam ao seu localizador de streaming. Para obter mais informações, consulte [associar filtros ao localizador de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Fevereiro de 2019

O Media Services V3 agora tem suporte nas nuvens nacionais do Azure. Nem todos os recursos estão disponíveis em todas as nuvens. Para obter mais detalhes, confira [Nuvens e regiões em que os Serviços de Mídia do Azure v3 existem](azure-clouds-regions.md).

O evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos esquemas da Grade de Eventos do Azure para Serviços de Mídia.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Arquivos do Media Encoder Standard e MPI 

Ao codificar usando o Media Encoder Standard para produzir arquivos MP4, um novo arquivo .mpi será gerado e adicionado ao Ativo de saída. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

Você não deve modificar nem remover o arquivo MPI, nem usar qualquer dependência em seu serviço na existência (ou não) desse arquivo.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações da versão disponível ao público geral da API V3 incluem:
       
* As propriedades **PresentationTimeRange** não são mais “obrigatórias” para **Filtros de Ativo** e **Filtros de Conta**. 
* As opções de consulta $top e $skip para **Trabalhos** e **Transformações** foram removidas e $orderby foi adicionado. Como parte da adição da nova funcionalidade de ordenação, foi descoberto que as opções $top e $skip acidentalmente tinham sido expostas anteriormente, embora não tenham sido implementadas.
* A extensibilidade da enumeração foi reabilitada. Esse recurso estava habilitado nas versões prévias do SDK e foi acidentalmente desabilitado na versão disponível ao público geral.
* Duas políticas predefinidas de transmissão foram renomeadas. **SecureStreaming** agora é **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** agora é **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

O módulo de CLI 2.0 agora está disponível para [serviços de mídia do Azure v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [conta do ams AZ](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [filtro de conta do ams AZ](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [ativo do ams AZ](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [ativos de ams AZ-filtro](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ ams chave de política de conteúdo](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [trabalho de ams AZ](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ ams-evento ao vivo](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [ams AZ saída ao vivo](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ ams streaming-ponto de extremidade](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ ams-localizador de streaming](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) – permite que você gerencie Unidades Reservadas para Mídia. Para obter mais informações, confira [Dimensionar Unidades Reservadas para Mídia](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Novos recursos e alterações de quebra

#### <a name="asset-commands"></a>Comandos de ativos

- ```--storage-account``` e ```--container``` argumentos adicionados.
- Valores padrão para o tempo de expiração (Now + 23h) e permissões (Read) no comando ```az ams asset get-sas-url``` adicionado.

#### <a name="job-commands"></a>Comandos de trabalho

- ```--correlation-data``` e ```--label``` argumentos adicionados
- ```--output-asset-names``` foi renomeado para ```--output-assets```. Agora, ele aceita uma lista separada por espaços dos ativos no formato 'assetName=label'. Um ativo sem rótulo pode ser enviado assim: 'assetName ='.

#### <a name="streaming-locator-commands"></a>Comandos do Localizador de Fluxo

- O comando base ```az ams streaming locator``` foi substituído por ```az ams streaming-locator```.
- ```--streaming-locator-id``` e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument``` argumento atualizado.
- ```--content-policy-name``` foi renomeado para ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de Política de Fluxo

- O comando base ```az ams streaming policy``` foi substituído por ```az ams streaming-policy```.
- Suporte para parâmetros de criptografia em ```az ams streaming-policy create``` adicionado.

#### <a name="transform-commands"></a>Transformar comandos

- ```--preset-names``` argumento substituído por ```--preset```. Agora, você só pode definir uma saída/predefinição de cada vez (para adicionar mais, é preciso executar ```az ams transform output add```). Além disso, você pode definir o StandardEncoderPreset personalizado passando o caminho para seu JSON personalizado.
- ```az ams transform output remove``` pode ser executado passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumentos adicionados no ```az ams transform create``` e ```az ams transform output add``` comandos.

## <a name="october-2018---ga"></a>Outubro de 2018 - GA

Esta seção descreve as atualizações de outubro do AMS (Serviços de Mídia do Azure).

### <a name="rest-v3-ga-release"></a>Versão GA v3 de REST

A [versão GA v3 de REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para Live, filtros de manifesto de nível de Conta/Ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gerenciamento de Recursos do Azure 

O suporte para o Gerenciamento de Recursos do Azure permite o gerenciamento unificado e a API de operações (agora, tudo em um só lugar).

A partir desta versão, é possível usar modelos do Resource Manager para criar Eventos ao Vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de Ativos 

As seguintes melhorias foram introduzidas:

- Ingestão de URLs HTTP(s) ou URLs de SAS do Armazenamento de Blobs do Azure.
- Especifique seus próprios nomes de contêiner para os Ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com o Azure Functions.

#### <a name="new-transform-object"></a>Novo objeto de Transformação

O novo objeto de **Transformação** simplifica o modelo de Codificação. O novo objeto facilita a criação e o compartilhamento de modelos e predefinições de codificação do Resource Manager. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação do Azure Active Directory e RBAC

A Autenticação do Azure AD e o RBAC (Controle de Acesso Baseado em Função) permitem Transformações seguras, LiveEvents, Políticas de Chave de Conteúdo, Ativos por Função ou Usuários no Azure AD.

#### <a name="client-sdks"></a>SDKs do cliente  

Linguagens com suporte nos Serviços de Mídia v3: .NET Core, Java, Node.js, Ruby, Datilografado, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

As seguintes atualizações de codificação ao vivo são apresentadas:

- Novo modo de baixa latência para ao vivo (10 segundos de ponta a ponta).
- Suporte aprimorado do RTMP (maior estabilidade e mais suporte de codificador de código-fonte).
- Ingestão segura de RTMPS.

    Ao criar um evento ao vivo, você receberá quatro URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId) e apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS. 
- Suporte de transcodificação 24 horas por dia. 
- Suporte aprimorado de sinalização de anúncio no RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte aprimorado a Grade de Eventos

É possível ver os seguintes aprimoramentos de suporte à Grade de Eventos:

- Integração à Grade de Eventos do Azure para facilitar o desenvolvimento com os Aplicativos Lógicos e o Azure Functions. 
- Inscreva-se para eventos sobre Codificação, Canais ao vivo, e muito mais.

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de criptografia CMAF e 'cbcs' para players Apple HLS (iOS 11+) e MPEG-DASH que dão suporte a CMAF.

### <a name="video-indexer"></a>Video Indexer

A versão de GA do Video Indexer foi anunciada em agosto. Para obter novas informações sobre recursos atualmente com suporte, consulte [O que é Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planos de alterações

#### <a name="azure-cli-20"></a>CLI do Azure 2.0
 
O módulo da CLI 2.0 do Azure que inclui operações em todos os recursos (incluindo Live, Políticas de Chave de Conteúdo, Filtros de Conta/Ativo, Políticas de Streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de versão prévia para material ativo ou AccountFilters são afetados pelo problema a seguir.

Se você criou Ativos ou Filtros de Conta entre 09/28 e 10/12 com CLI ou APIs dos Serviços de Mídia v3, será necessário remover todos os Ativos e AccountFilters e recriá-los devido a um conflito de versões. 

## <a name="may-2018---preview"></a>Maio de 2018 - Versão prévia

### <a name="net-sdk"></a>.NET SDK

Os seguintes recursos estão presentes no SDK do .NET:

* **Transformações** e **Trabalhos** para codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Transmitir arquivos por stream](stream-files-tutorial-with-api.md) e [Analisar](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicar e transmitir conteúdo aos dispositivos do usuário final
* **Políticas de streaming** e **Políticas de chave de conteúdo** para configurar a distribuição de chaves e o DRM (gerenciamento de direitos digitais) durante a entrega de conteúdo.
* **Eventos ao vivo** e **Saídas ao vivo** para configurar a ingestão e o arquivamento de conteúdo de transmissão ao vivo.
* **Ativos** para armazenar e publicar o conteúdo de mídia no Armazenamento do Azure. 
* **Pontos de extremidade de streaming** para configurar e dimensionar o empacotamento dinâmico, a criptografia e o streaming de conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar para ingerir o vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, o AMS v3 não oferece suporte à codificação de transferência em partes sobre URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximos passos

- [Visão geral](media-services-overview.md)
- [Notas de versão do Media Services v2](../previous/media-services-release-notes.md)
