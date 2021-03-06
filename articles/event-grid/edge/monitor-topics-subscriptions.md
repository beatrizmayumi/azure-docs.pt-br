---
title: Monitorar tópicos e assinaturas de evento-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Monitorar tópicos e assinaturas de evento
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 79b223de7a0a0cfdaf799b1f80e585a2a55f7e82
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849728"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorar tópicos e assinaturas de evento

A grade de eventos no Edge expõe um número de métricas para tópicos e assinaturas de evento no [formato Prometheus exposição](https://prometheus.io/docs/instrumenting/exposition_formats/). Este artigo descreve as métricas disponíveis e como habilitá-las.

## <a name="enable-metrics"></a>Habilitar a métrica

Configure o módulo para emitir métricas definindo a variável de ambiente `metrics__reporterType` como `prometheus` nas opções de criação de contêiner:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

As métricas estarão disponíveis em `5888/metrics` do módulo para http e `4438/metrics` para https. Por exemplo, `http://<modulename>:4438/metrics?api-version=2019-01-01-preview` para http. Neste ponto, um módulo de métricas pode sondar o ponto de extremidade para coletar métricas como nesta [arquitetura de exemplo](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métricas disponíveis

Os tópicos e as assinaturas de evento emitem as métricas para fornecer informações sobre a entrega de eventos e o desempenho do módulo.

### <a name="topic-metrics"></a>Métricas de tópico

| Métrica | Description |
| ------ | ----------- |
| EventsReceived | Número de eventos publicados no tópico
| UnmatchedEvents | Número de eventos publicados no tópico que não correspondem a uma assinatura de evento e são removidos
| SuccessRequests | Número de solicitações de publicação de entrada recebidas pelo tópico
| SystemErrorRequests | Número de solicitações de publicação de entrada com falha devido a um erro interno do sistema
| UserErrorRequests | Número de solicitações de publicação de entrada com falha devido a um erro do usuário, como JSON malformado
| SuccessRequestLatencyMs | Latência de resposta de solicitação de publicação em milissegundos


### <a name="event-subscription-metrics"></a>Métricas de assinatura de evento

| Métrica | Description |
| ------ | ----------- |
| deliverySuccessCounts | Número de eventos entregues com êxito ao ponto de extremidade configurado
| deliveryFailureCounts | Número de tentativas de entrega de eventos com falha no ponto de extremidade configurado
| deliverySuccessLatencyMs | Latência de eventos entregues com êxito em milissegundos
| deliveryFailureLatencyMs | Latência de falhas de entrega de eventos em milissegundos
| systemDelayForFirstAttemptMs | Atraso do sistema de eventos antes da primeira tentativa de entrega em milissegundos
| deliveryAttemptsCount | Número de tentativas de entrega de eventos-êxito e falha
| expiredCounts | Número de eventos que não podem ser entregues 