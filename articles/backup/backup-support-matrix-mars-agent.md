---
title: Matriz de suporte para o agente MARS
description: Este artigo resume o suporte ao backup do Azure ao fazer backup de computadores que executam o agente de Serviços de Recuperação do Microsoft Azure (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: a87d778bff5a52f4251d83e401028c9949713e33
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988066"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente MARS (Serviços de Recuperação do Microsoft Azure)

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de computadores e aplicativos locais e fazer backup de VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações quando você usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup de computadores.

## <a name="the-mars-agent"></a>O agente MARS

O backup do Azure usa o agente MARS para fazer backup de dados de máquinas locais e VMs do Azure para um cofre de serviços de recuperação de backup no Azure. O agente MARS pode:

- Execute em computadores Windows locais para que eles possam fazer backup diretamente em um cofre de serviços de recuperação de backup no Azure.
- Execute em VMs do Windows para que eles possam fazer backup diretamente em um cofre.
- Execute no Backup do Microsoft Azure Server (MABS) ou um servidor do System Center Data Protection Manager (DPM). Nesse cenário, as máquinas e as cargas de trabalho fazem backup em MABS ou no servidor DPM. O agente MARS faz o backup desse servidor para um cofre no Azure.

> [!NOTE]
>O backup do Azure não dá suporte ao ajuste automático do relógio para horário de Verão (DST). Modifique a política para garantir que a economia de horário de verão seja levada em conta para evitar discrepâncias entre a hora real e o horário de backup agendado.

Suas opções de backup dependem de onde o agente está instalado. Para obter mais informações, consulte [arquitetura de backup do Azure usando o agente Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup do MABS e do DPM, consulte [fazer backup para o DPM ou mAbs](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [os requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de backup.

**Instalação** | **Detalhes**
--- | ---
Baixe o agente MARS mais recente | Você pode baixar do cofre a versão mais recente do agente ou [baixá-lo diretamente](https://aka.ms/azurebackup_agent).
Instalar diretamente em um computador | Você pode instalar o agente MARS diretamente em um Windows Server local ou em uma VM do Windows que esteja executando qualquer um dos [sistemas operacionais com suporte](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalar em um servidor de backup | Ao configurar o DPM ou MABS para fazer backup no Azure, você baixa e instala o agente MARS no servidor. Você pode instalar o agente em [sistemas operacionais com suporte](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por padrão, as VMs do Azure que estão habilitadas para backup têm uma instalação de extensão de backup do Azure. Essa extensão faz backup de toda a VM. Você poderá instalar e executar o agente MARS em uma VM do Azure junto com a extensão se quiser fazer backup de pastas e arquivos específicos, em vez da VM completa.
> Quando você executa o agente MARS em uma VM do Azure, ele faz backup de arquivos ou pastas que estão no armazenamento temporário na VM. Os backups falharão se os arquivos ou pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.

## <a name="cache-folder-support"></a>Suporte a pasta de cache

Quando você usa o agente MARS para fazer backup de dados, o agente tira um instantâneo dos dados e os armazena em uma pasta de cache local antes de enviar os dados para o Azure. A pasta cache (Scratch) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  O espaço livre na pasta de cache deve ter pelo menos 5 a 10% do tamanho geral dos dados de backup.
Local | A pasta de cache deve ser armazenada localmente no computador que está sendo submetido a backup e deve estar online. A pasta de cache não deve estar em um compartilhamento de rede, em mídia removível ou em um volume offline.
Pasta | A pasta de cache não deve ser criptografada em um volume com eliminação de duplicação ou em uma pasta compactada, que seja esparsa ou que tenha um ponto de nova análise.
Alterações de local | Você pode alterar o local do cache interrompendo o mecanismo de backup (`net stop bengine`) e copiando a pasta de cache para uma nova unidade. (Verifique se a nova unidade tem espaço suficiente.) Em seguida, atualize duas entradas de registro em **HKLM\Software\Microsoft\Windows Azure backup** (**config/ScratchLocation** e **config/CloudBackupProvider/ScratchLocation**) para o novo local e reinicie o mecanismo.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-and-ip-access"></a>URL e acesso a IP

O agente MARS precisa de acesso a estas URLs:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

E para estes endereços IP:

- 20.190.128.0/18
- 40.126.0.0/18

O acesso a todas as URLs e endereços IP listados acima usa o protocolo HTTPS na porta 443.

### <a name="throttling-support"></a>Suporte de limitação

**Recurso** | **Detalhes**
--- | ---
Controle de Largura de Banda | Com suporte. No agente MARS, use **alterar propriedades** para ajustar a largura de banda.
Limitação de rede | Não disponível para computadores com backup que executam o Windows Server 2008 R2, o Windows Server 2008 SP2 ou o Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para backups diretos

>[!NOTE]
> O agente MARS não dá suporte a SKUs do Windows Server Core.

Você pode usar o agente MARS para fazer backup diretamente no Azure nos sistemas operacionais listados abaixo que são executados em:

1. Servidores locais do Windows
2. VMs do Azure que executam o Windows

Os sistemas operacionais devem ser de 64 bits e devem estar executando os serviços e atualizações mais recentes. A tabela a seguir resume estes sistemas operacionais:

**Sistema operacional** | **Arquivos/pastas** | **Estado do sistema** | **Requisitos de software/módulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não |  Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows 8.1 (Enterprise, Pro)| Sim |Não | Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows 8 (Enterprise, Pro) | Sim | Não | Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Básico, Starter) | Sim | Não | Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sim | Sim | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0
Windows Server 2012 (Standard, Datacenter, Foundation) | Sim | Sim |-.NET 4,5 <br> \- Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0 <br> -Gerenciamento e manutenção de imagens de implantação (DISM. exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sim | Não | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sim | Sim | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0

Para obter mais informações, consulte [supported mAbs and DPM Operating Systems](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites do Backup

### <a name="size-limits"></a>Limites de tamanho

O backup do Azure limita o tamanho de uma fonte de dados de arquivo ou pasta cujo backup pode ser feito. Os itens que você faz backup de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema operacional** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |54.400 GB
Windows Server 2008 R2 SP1 |1\.700 GB
Windows Server 2008 SP2| 1\.700 GB
Windows 8 ou superior| 54.400 GB
Windows 7| 1\.700 GB

### <a name="other-limitations"></a>Outras limitações

- O MARS não oferece suporte à proteção de vários computadores com o mesmo nome para um único cofre.

## <a name="supported-file-types-for-backup"></a>Tipos de arquivo compatíveis para backup

**Tipo** | **Suporte**
--- | ---
Criptografado| Com suporte.
Compressed | Com suporte.
Esparsos | Com suporte.
Compactados e esparsos |Com suporte.
Links físicos| Sem suporte. Ignorada.
Ponto de nova análise| Sem suporte. Ignorada.
Criptografados e esparsos |Sem suporte. Ignorada.
Fluxo compactado| Sem suporte. Ignorada.
Fluxo esparso| Sem suporte. Ignorada.
OneDrive (arquivos sincronizados são fluxos esparsos)| Sem suporte.

## <a name="supported-drives-or-volumes-for-backup"></a>Unidades ou volumes com suporte para backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes somente leitura| Sem suporte | O VSS (serviço de cópias de sombra de volume) só funcionará se o volume for gravável.
Volumes offline| Sem suporte |O VSS só funcionará se o volume estiver online.
Compartilhamento de rede| Sem suporte |O volume deve ser local no servidor.
Volumes bloqueados pelo BitLocker| Sem suporte |O volume deve ser desbloqueado antes do início do backup.
Identificação do sistema de arquivos| Sem suporte |Há suporte apenas para NTFS.
Mídia removível| Sem suporte |Todas as fontes de itens de backup devem ter um status *fixo* .
Unidades com eliminação de duplicação | Com suporte | O Backup do Azure converte dados com eliminação de duplicação em dados normais. Ele otimiza, criptografa, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para o backup offline inicial

O backup do Azure dá suporte à *propagação offline* para transferir dados de backup iniciais para o Azure usando discos. Esse suporte será útil se o backup inicial provavelmente estiver na faixa de tamanho de terabytes (TBs). O backup offline é compatível com:

- Backup direto de arquivos e pastas em computadores locais que estão executando o agente MARS.
- Backup de cargas de trabalho e arquivos de um servidor DPM ou MABS.

O backup offline não pode ser usado para arquivos de estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para restauração de dados

Usando o recurso de [restauração instantânea](backup-instant-restore-capability.md) do backup do Azure, você pode restaurar os dados antes que eles sejam copiados para o cofre. O computador no qual você está fazendo backup deve estar em execução .NET Framework 4.5.2 ou superior.

Os backups não podem ser restaurados em um computador de destino que esteja executando uma versão anterior do sistema operacional. Por exemplo, um backup feito de um computador que executa o Windows 7 pode ser restaurado no Windows 8 ou posterior. Mas um backup feito de um computador que executa o Windows 8 não pode ser restaurado em um computador que esteja executando o Windows 7.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a arquitetura de backup que usa o agente Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Saiba o que tem suporte quando você [executa o agente Mars no mAbs ou em um servidor DPM](backup-support-matrix-mabs-dpm.md).
