---
title: Monitoramento-banco de dados do Azure para MariaDB
description: Este artigo descreve as métricas de monitoramento e alerta do Banco de Dados do Azure para MariaDB, incluindo estatísticas de CPU, armazenamento e conexão.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 15a396a86103f41f49d3b49878ec51c1e71add40
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772472"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorando no Banco de Dados do Azure para MariaDB
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para MariaDB fornece várias métricas que fornecem informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-metric.md) (Como configurar alertas).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para MariaDB:

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|A porcentagem de armazenamento de log do servidor usada fora do armazenamento de log máximo do servidor.|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|O armazenamento de log do servidor de máximo para esse servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Conexões com falha|Contagem|O número de conexões com falha com o servidor.|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|

## <a name="server-logs"></a>Logs do servidor

É possível habilitar o registro de consulta no servidor. Esses logs também estão disponíveis por meio dos logs de diagnóstico do Azure em logs de Azure Monitor, hubs de eventos e conta de armazenamento. Para saber mais sobre o registro, visite a página  [logs do servidor](concepts-server-logs.md).

## <a name="query-store"></a>Repositório de Consultas

[Repositório de consultas](concepts-query-store.md) controla o desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de execução de consulta e eventos de espera. O recurso persiste as informações de desempenho de tempo de execução de consulta no esquema **MySQL** . Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Informações de desempenho de consulta

[Análise de Desempenho de Consultas](concepts-query-performance-insight.md) funciona em conjunto com o Repositório de Consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. Análise de Desempenho de Consultas é acessível na seção de **desempenho inteligente** da página do portal do banco de dados do Azure para o MariaDB Server.

## <a name="performance-recommendations"></a>Recomendações de desempenho

O recurso [Recomendações de Desempenho](concepts-performance-recommendations.md) identifica as oportunidades de melhorar o desempenho da carga de trabalho. As recomendações de desempenho fornecem recomendações para a criação de novos índices que têm o potencial de melhorar o desempenho de suas cargas de trabalho. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="service-health"></a>Integridade do serviço
A [integridade do serviço do Azure](../service-health/overview.md) fornece uma exibição de todas as notificações de integridade do serviço em sua assinatura. Você pode configurar alertas de integridade do serviço para notificá-lo por meio de seus canais de comunicação preferenciais quando houver problemas ou alterações que possam afetar os serviços e as regiões do Azure que você usa.

Você pode exibir eventos de manutenção agendados para o banco de dados do Azure para MariaDB usando o tipo de evento de **manutenção planejada** . Para saber como criar **alertas de integridade do serviço**, visite o artigo [criar alertas do log de atividades em notificações de serviço](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> As notificações de manutenção planejada estão disponíveis em versão prévia para o leste dos EUA e apenas Sul do Reino Unido.

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Consulte [Como configurar alertas](howto-alert-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
