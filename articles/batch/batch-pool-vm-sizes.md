---
title: Escolher tamanhos de VM para pools - Azure Batch | Microsoft Docs
description: Como escolher entre os tamanhos de VM disponíveis para nós de computação em pools de Lote do Azure
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: be19de19dab92bc40ca5529ad578e033a98929cd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023558"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolher um tamanho de VM para nós de computação em um pool do Lote do Azure

Ao selecionar um tamanho de nó para um pool de Lote do Azure, você pode escolher dentre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para VMs do Linux e do Windows para diferentes cargas de trabalho.

Há algumas exceções e limitações para escolher um tamanho de VM:

* Não há suporte para alguns tamanhos de VM ou séries de VM no lote.
* Alguns tamanhos de VM são restritos e precisam ser especificamente habilitados antes de serem alocados.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos de VM com suporte

### <a name="pools-in-virtual-machine-configuration"></a>Pools na configuração de Máquina Virtual

Os pools do lote na configuração de máquina virtual dão suporte a quase todos os tamanhos de VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Consulte a tabela a seguir para saber mais sobre tamanhos e restrições com suporte.

Os tamanhos de VM promocional ou de visualização não listados não são garantidos para suporte.

| Série da VM  | Tamanhos com suporte | Modo de alocação do pool de contas do lote<sup>1</sup> |
|------------|---------|-----------------|
| Série A básica | Todos os tamanhos *, exceto* Basic_A0 (a0) | Qualquer |
| Séria A | Todos os tamanhos *, exceto* Standard_A0 | Qualquer |
| Série Av2 | Todos os tamanhos | Qualquer |
| Série B | Nenhum | Não disponível |
| Série DC | Nenhum | Não disponível |
| Dv2, série DSv2 | Todos os tamanhos | Qualquer |
| Dv3, série Dsv3 | Todos os tamanhos | Qualquer |
| Ev3, série Esv3 | Todos os tamanhos | Qualquer |
| Série Fsv2 | Todos os tamanhos | Qualquer |
| Série H | Todos os tamanhos | Qualquer |
| Série HB<sup>2</sup> | Todos os tamanhos | Qualquer |
| HC-série<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série Ls | Todos os tamanhos | Qualquer |
| Série Lsv2 | Nenhum | Não disponível |
| Série M | Standard_M64ms (somente baixa prioridade), Standard_M128s (somente baixa prioridade) | Qualquer |
| Série Mv2 | Nenhum | Não disponível |
| Série NC | Todos os tamanhos | Qualquer |
| Série NCv2<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série NCv3<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série ND<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série NDv2 | Todos os tamanhos | Modo de assinatura do usuário |
| Série NV | Todos os tamanhos | Qualquer |
| Série NVv3 | Nenhum | Não disponível |
| SAP HANA | Nenhum | Não disponível |

<sup>1</sup> há suporte parcial para algumas séries VM mais recentes inicialmente. Essas séries de VMs podem ser alocadas por contas do lote com o **modo de alocação do pool** definido como assinatura do **usuário**. Consulte [gerenciar contas do lote](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) para obter mais informações sobre a configuração da conta do lote. Consulte [cotas e limites](batch-quota-limit.md) para saber como solicitar cota para essas séries de VMs com suporte parcial para contas do lote de **assinatura do usuário** .  

<sup>2</sup> esses tamanhos de VM podem ser alocados em pools do lote na configuração da máquina virtual, mas você deve solicitar um [aumento de cota](batch-quota-limit.md#increase-a-quota)específico.

### <a name="pools-in-cloud-service-configuration"></a>Pools na configuração de Serviço de Nuvem

Os pools do lote na configuração do serviço de nuvem dão suporte a todos os [tamanhos de VM para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md) **, exceto** para o seguinte:

| Série da VM  | Tamanhos sem suporte |
|------------|-------------------|
| Séria A   | Extrapequena       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações de tamanhos

* **Requisitos do aplicativo** - considere as características e os requisitos dos aplicativos que você vai executar nos nós. Os aspectos como se o aplicativo tem multithread e quanta memória ele consome podem ajudar a determinar o tamanho do nó mais adequado e econômico. Para [cargas de trabalho de MPI](batch-mpi.md) de instâncias múltiplas ou aplicativos CUDA, considere tamanhos de VM de [HPC](../virtual-machines/linux/sizes-hpc.md) especializado ou [habilitado para GPU](../virtual-machines/linux/sizes-gpu.md), respectivamente. (Confira [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md).)

* **Tarefas por nó** - Geralmente, você seleciona um tamanho de nó supondo que uma tarefa seja executada no nó por vez. No entanto, pode ser vantajoso ter várias tarefas (e, portanto, várias instâncias do aplicativo) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução do trabalho. Nesse caso, é comum escolher um tamanho multicore de nó para acomodar a demanda crescente de execução de tarefas paralelas.

* **Carregar níveis para tarefas diferentes** - Todos os nós em um pool têm o mesmo tamanho. Se você pretende executar aplicativos com diferentes requisitos de sistema e/ou níveis de carga, é recomendável usar pools separados.

* **Disponibilidade de região** – uma série ou tamanho de VM pode não estar disponível nas regiões em que você cria suas contas do lote. Para verificar se um tamanho está disponível, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* **Cotas** - As [cotas principais](batch-quota-limit.md#resource-quotas) na sua conta do Lote podem limitar o número de nós de um determinado tamanho que você pode adicionar a um pool do Lote. Para solicitar um aumento de cota, consulte [este artigo](batch-quota-limit.md#increase-a-quota). 

* **Configuração do pool** - Em geral, você tem mais opções de tamanho de VM quando você cria um pool na configuração da Máquina Virtual, em comparação com a configuração do serviço de nuvem.

## <a name="next-steps"></a>Próximos passos

* Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
* Para obter informações sobre tamanhos de VM de computação intensiva, consulte [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md).
