---
title: Matriz de suporte para migrações para Azure
description: Fornece um resumo das configurações de suporte e limitações para o serviço migrações para Azure.
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 5c3adecf62f9bd15c820baf116dbc01e5d3542fa
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014004"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte para migrações para Azure

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte geral e as limitações para cenários e implantações de migração do Azure.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração com suporte

A tabela resume os cenários de descoberta, avaliação e migração com suporte.

**Implantação** | **Detalhes** 
--- | --- 
**Descoberta específica do aplicativo** | Você pode descobrir aplicativos, funções e recursos em execução em VMs VMware. Atualmente, esse recurso está limitado apenas à descoberta. A avaliação está atualmente no nível do computador. Ainda não oferecemos aplicativo, função ou avaliação específica de recurso. 
**Avaliação local** | Avalie cargas de trabalho locais e dados em execução em VMs VMware, VMs Hyper-V e servidores físicos. Avalie o uso do DMA (avaliação de servidor e Assistente de Migração de Dados da Microsoft) de migrações do Azure, bem como outras ferramentas e ofertas de ISV.
**Migração local para o Azure** | Migre cargas de trabalho e dados em execução em servidores físicos, VMs VMware, VMs Hyper-V, servidores físicos e VMS baseadas em nuvem para o Azure. Migre usando a avaliação de servidor de migrações para Azure e o serviço de migração de banco de dados do Azure (DMS), bem como outras ferramentas e ofertas de ISV.


## <a name="supported-tools"></a>Ferramentas com suporte

O suporte a ferramentas específicas é resumido na tabela.

**Ferramenta** | **Avaliar** | **Migrar** 
--- | --- | ---
Avaliação de Servidor das Migrações para Azure | Avalie [VMs VMware](tutorial-prepare-vmware.md), [VMs Hyper-V](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md). |  Não disponível (NA)
Migração de Servidor das Migrações para Azure | ND | Migre [VMs VMware](tutorial-migrate-vmware.md), [VMs Hyper-V](tutorial-migrate-hyper-v.md)e [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | ND | Migre VMs VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública. | ND
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Avaliar e migrar VMs VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública. |  Migre VMs VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública.| ND
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Avaliar os bancos de dados SQL Server locais. | ND
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | ND | Migre SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avalie o Virtual Desktop Infrastructure (VDI) | ND
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Avaliar VMs do VMWare, VMs do Hyper-V, VMs Xen, máquinas físicas, estações de trabalho (incluindo VDI), cargas de trabalho de nuvem pública | ND
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | ND | Migrar VMs VMWare, VMs do Hyper-V, VMS Xen, VMs KVM, máquinas físicas, cargas de trabalho de nuvem pública 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública. | ND
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos, cargas de trabalho de nuvem pública e bancos de dados SQL Server. | ND
[Assistente de Migração webapp](https://appmigration.microsoft.com/) | Avaliar aplicativos Web | Migre aplicativos Web.


## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Subscription | Você pode ter vários projetos de migrações para Azure em uma assinatura.
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
VMs VMware  | Avalie até 35.000 VMs VMware em um único projeto.
VMs Hyper-V | Avalie até 35.000 VMs do Hyper-V em um único projeto.

Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.

## <a name="azure-permissions"></a>Permissões do Azure

Para que as migrações para Azure funcionem com o Azure, você precisa dessas permissões antes de começar a avaliar e migrar computadores.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto das Migrações para Azure | Sua conta do Azure precisa de permissões para criar um projeto. | Configurar para [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)ou [servidores físicos](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Registrar o dispositivo de migrações para Azure| As migrações para Azure usam um dispositivo leve de [migrações para Azure](migrate-appliance.md) para avaliar máquinas com a avaliação de servidor de migrações para Azure e para executar a [migração sem agente](server-migrate-overview.md) de VMs VMware com migração de servidor de migrações para Azure Esse dispositivo descobre computadores e envia metadados e dados de desempenho para migrações para Azure.<br/><br/> Durante o registro, os provedores de registro (Microsoft. OffAzure, Microsoft. migre e Microsoft. keyvault) são registrados com a assinatura escolhida no dispositivo, para que a assinatura funcione com o provedor de recursos. Para registrar-se, você precisa de acesso de colaborador ou proprietário na assinatura.<br/><br/> **VMware**– durante a integração, as migrações para Azure criam dois aplicativos Azure Active Directory (Azure AD). O primeiro aplicativo se comunica entre os agentes de dispositivo e o serviço de migrações para Azure. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso de RBAC para recursos. O segundo aplicativo acessa um Azure Key Vault criado na assinatura do usuário somente para a migração do VMware sem agente. Na migração sem agente, as migrações para Azure criam um Key Vault para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Ele tem acesso de RBAC no Azure Key Vault (no locatário do cliente) quando a descoberta é iniciada a partir do dispositivo.<br/><br/> **Hyper-V**– durante a integração. As migrações para Azure criam um aplicativo do Azure AD. O aplicativo se comunica entre os agentes de dispositivo e o serviço de migrações para Azure. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso de RBAC para recursos. | Configurar para [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)ou [servidores físicos](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Criar um cofre de chaves para migração sem agente do VMware | Para migrar VMs VMware com migração de servidor do Azure Migrations sem agente, as migrações para Azure criam um Key Vault para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você define permissões (proprietário, ou colaborador e administrador de acesso do usuário) no grupo de recursos no qual o projeto de migração do Azure reside. | [Configurar](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) permissões.

## <a name="supported-geographies"></a>Geografia com suporte

Você pode criar um projeto de migrações para Azure em uma série de geografias. Embora você só possa criar projetos nessas regiões, você pode avaliar ou migrar computadores para outros locais de destino. A região geográfica do projeto é usada apenas para armazenar os metadados descobertos.

**Geografia** | **Local de armazenamento de metadados**
--- | ---
Azure Governamental | US Gov - Virgínia
Pacífico Asiático | Ásia Oriental ou sudeste asiático
Austrália | Leste da Austrália ou sudeste da Austrália
Brasil | Sul do Brasil
Canadá | Canadá central ou leste do Canadá
Europa | Norte da Europa ou Europa Ocidental
França | França Central
Índia | Índia central ou sul da Índia
Japão |  Leste do Japão ou oeste do Japão
Coreia do Sul | Coreia central ou sul da Coreia
Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
Estados Unidos | EUA Central ou oeste dos EUA 2


 > [!NOTE]
 > No momento, o suporte para Azure governamental está disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) das migrações para Azure.



## <a name="vmware-assessment-and-migration"></a>Avaliação e migração do VMware

[Reveja](migrate-support-matrix-vmware.md) a matriz de suporte de migração de servidor e avaliação do Azure Migrations para VMs VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Revise](migrate-support-matrix-hyper-v.md) a matriz de suporte de migração de servidor e avaliação do Azure migrações para VMs do Hyper-V.



## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: usando esta versão, você pode criar novos projetos de migrações para Azure, descobrir avaliações locais e orquestrações e migrações. [Saiba mais](whats-new.md#release-version-july-2019).
- **Versão anterior**: para o cliente que usa a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos de migrações para Azure ou executar novas descobertas.

## <a name="next-steps"></a>Próximos passos

- [Avalie as VMs do VMware](tutorial-assess-vmware.md) para migração.
- [Avalie as VMs do Hyper-V](tutorial-assess-hyper-v.md) para migração.

