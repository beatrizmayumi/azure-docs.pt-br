---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 09f379279a7247f87b9e0830414a5e4363f41cdb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469679"
---
Os tamanhos de VM otimizados para memória oferecem uma taxa de memória alta para CPU que são ideais para servidores de banco de dados relacionais, caches médio a grande e análises in-memory. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

* A série Ev3 apresenta os processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral e trazendo o Ev3 para alinhamento com as VMs de uso geral da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de rede e disco foram ajustados em uma base por núcleo para alinhar com a mudança para o hyperthreading.  A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

* A série Eav4 e a Easv4 utilizam o processador de 2.35 GHz EPYC<sup>TM</sup> 7452 em uma configuração multi-threaded com até 256 MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho com otimização de memória.  As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que o Ev3 & série Esv3.

* A série Mv2 oferece a contagem de vCPU mais alta (até 416 vCPUs) e a maior memória (até 8,19 TiB) de qualquer VM na nuvem. Ele é ideal para bancos de dados muito grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e de grandes quantidades de memória.

* A série M oferece uma contagem alta de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Ele também é ideal para bancos de dados muito grandes ou outros aplicativos que se beneficiam de contagens vCPUs altas e grandes quantidades de memória.

* As Dv2, série G e as contrapartes DSv2/GS são ideais para aplicativos que exigem mais rapidez vCPUs, melhor desempenho de armazenamento temporário ou têm mais demandas de memória. Elas oferecem uma potente combinação para aplicativos de nível empresarial.

* A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A série Dv2 é de cerca de 35% mais rápida do que a série D. Ele é executado nos processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) e com a tecnologia Intel Turbo Boost 2,0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

* A Computação do Azure oferece tamanhos de máquinas virtuais que são isoladas para um tipo de hardware específico e que são dedicadas a um único cliente.  Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Consulte as tabelas de famílias de máquina virtual abaixo para ver as opções de VM isoladas.

## <a name="esv3-series"></a>Série Esv3

ACU: 160-190 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As instâncias da série ESv3 apresentam os processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e podem atingir 3,5 GHz com a tecnologia Intel Turbo Boost 2,0 e usar o armazenamento Premium. As instâncias Ev3 são ideais para aplicativos empresariais com uso intensivo de memória.


| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM Esv3-series.

<sup>2</sup> Tamanhos limitados de núcleos disponíveis.

<sup>3</sup> A instância é isolada em hardware dedicado a um único cliente.

## <a name="easv4-series"></a>Série Easv4

ACU: 230-260

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Os tamanhos da série Easv4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar uma frequência máxima aumentada de 3.35 GHz e usar SSD Premium. Os tamanhos da série Easv4 são ideais para aplicativos empresariais com uso intensivo de memória.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/largura de banda de rede esperada (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | | 
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | | 
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Esses tamanhos estão em versão prévia. Se você estiver interessado em experimentar esses tamanhos maiores, Inscreva-se em [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="ev3-series"></a>Série Ev3 

ACU: 160 - 190 <sup>1</sup>

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

As instâncias da série Ev3 apresentam os processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e podem atingir 3,5 GHz com a tecnologia Intel Turbo Boost 2,0. As instâncias Ev3 são ideais para aplicativos empresariais com uso intensivo de memória.

O armazenamento do disco de dados é cobrado separadamente das máquinas virtuais. Para usar discos de armazenamento premium, use os tamanhos ESv3. Os medidores de cobrança e preço para os tamanhos Esv3 são os mesmos da Ev3-series. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | NICs máximas / largura de banda da rede |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM Ev3-series.

<sup>2</sup> Tamanhos limitados de núcleos disponíveis.

<sup>3</sup> A instância é isolada em hardware dedicado a um único cliente.

## <a name="eav4-series"></a>Série Eav4

ACU: 230-260

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

Os tamanhos da série Eav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar uma frequência máxima aumentada de 3.35 GHz e usar SSD Premium. Os tamanhos da série Eav4 são ideais para aplicativos empresariais com uso intensivo de memória. O armazenamento do disco de dados é cobrado separadamente das máquinas virtuais. Para usar o SSD Premium, use os tamanhos da série Easv4. Os medidores de cobrança e preço para os tamanhos de Easv4 são os mesmos que os da série Eav3.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Máximo de NICs/largura de banda de rede esperada (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Standard\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Standard\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standard\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standard\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| Standard\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| Standard\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standard\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standard\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Esses tamanhos estão em versão prévia.  Se você estiver interessado em experimentar esses tamanhos maiores, Inscreva-se em [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="mv2-series"></a>Série Mv2

ACU: 188-280<sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Acelerador de Gravação: [com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

A Mv2-Series apresenta alta taxa de transferência, plataforma de baixa latência em execução em um processador Hyper-Threading Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) com uma frequência base básica de 2,5 GHz e uma frequência máxima de Turbo de 3,8 GHz. Todos os tamanhos de máquina virtual da série Mv2 podem usar discos persistentes Standard e Premium. As instâncias da série Mv2 são tamanhos de VM com otimização de memória que fornecem desempenho computacional inigualável para dar suporte a grandes bancos de dados na memória e cargas de trabalho, com uma alta taxa de memória para CPU, ideal para servidores de banco de dados relacionais, caches grandes e na memória Analytics.

|Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> o recurso da VM da série Mv2 Intel® tecnologia Hyper-Threading

<sup>2</sup> as VMs da série Mv2 são apenas de geração 2. Se você estiver usando o Linux, consulte [suporte para VMs de geração 2 no Azure](../articles/virtual-machines/linux/generation-2.md) para obter instruções sobre como localizar e selecionar uma imagem.

<sup>3</sup> para os tamanhos de M416ms_v2 e M416s_v2, observe que há suporte inicial apenas para a imagem a seguir: "GEN2: SuSE Linux Enterprise Server (SLES) 12 SP4 para aplicativos SAP".

## <a name="m-series"></a>Série M 

ACU: 160-180 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Os tamanhos da série M são baseados no Intel (R) Xeon (R) CPU E7-8890 v3 @ 2,50 GHz   

Acelerador de gravação: [com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Tamanho            | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2\.048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2\.048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2\.048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2\.048        | 4096  | 64 | 160000/1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2\.048 | 14336 | 64 | 250000/1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM série M

<sup>2</sup> mais de 64 vCPU exigem um destes sistemas operacionais convidados com suporte: Windows Server 2016, Ubuntu 16, 4 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7,3 ou Oracle Linux 7,3 com Lis 4.2.1.

<sup>3</sup> Tamanhos limitados de núcleos disponíveis.

<sup>4</sup> A instância é isolada em hardware dedicado a um único cliente.
<br>


## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

ACU: 210 - 250 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2,1 GHz (Skylake) ou no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira o artigo [Projetar para um alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> a instância é isolada para o hardware baseado em Intel Haswell e dedicada a um único cliente.  
<sup>3</sup> Tamanhos limitados de núcleos disponíveis.  
<sup>4</sup> 25000 Mbps com Rede Acelerada. 

<br>

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

ACU: 210 - 250

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2,1 GHz (Skylake) ou no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Tamanho              | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1\.000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.  
<sup>2</sup> 25000 Mbps com Rede Acelerada. 
