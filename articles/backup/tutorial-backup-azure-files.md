---
title: Tutorial – Backup de compartilhamentos de Arquivos do Azure
description: Neste tutorial, saiba como usar o portal do Azure para configurar um cofre dos Serviços de Recuperação e fazer backup de compartilhamentos de arquivo do Azure.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293922"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Fazer backup de compartilhamentos de arquivo do Azure no portal do Azure

Este tutorial explica como usar o portal do Azure para fazer backup de [compartilhamentos de arquivos do Azure](../storage/files/storage-files-introduction.md).

Neste guia, você aprenderá a:
> [!div class="checklist"]
>
> * Configurar um cofre de Serviços de Recuperação para fazer backup de Arquivos do Azure
> * Executar um trabalho de backup sob demanda para criar um ponto de restauração

## <a name="prerequisites"></a>Prerequisites

Antes de fazer backup de um compartilhamento de arquivos do Azure, verifique se ele está presente em um dos [tipos de Conta de Armazenamento com suporte](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Depois fazer essa verificação, é possível proteger o compartilhamento dos arquivos.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações de backup do compartilhamento de arquivos do Azure durante a versão prévia

A cópia de segurança para compartilhamentos de arquivos do Azure está em versão prévia. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de uso geral v2. Não há suporte para os cenários de backup a seguir para compartilhamentos de arquivos do Azure:

* Não há nenhuma CLI disponível para a proteção de Arquivos do Azure usando o Backup do Azure.
* A quantidade máxima de backups agendados por dia é de um.
* A quantidade máxima de backups sob demanda por dia é de quatro.
* Use [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na conta de armazenamento para impedir a exclusão acidental de backups em seu cofre dos Serviços de Recuperação.
* Não exclua os instantâneos criados pelo Backup do Azure. A exclusão de instantâneos pode resultar na perda de pontos de recuperação e/ou em falhas de restauração.
* Não exclua os compartilhamentos de arquivos protegidos pelo Backup do Azure. A solução atual excluirá todos os instantâneos tirados pelo Backup do Azure após a exclusão do compartilhamento de arquivos e, portanto, perderá todos os pontos de restauração

O backup para Compartilhamentos de Arquivos do Azure nas contas de armazenamento com replicação de [armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md) (ZRS) está disponível apenas no Centro dos EUA (CUS), Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Europa Setentrional (NE), Sudeste Asiático (SEA), Europa Ocidental (WE) e Oeste dos EUA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Configurar o backup para um compartilhamento de arquivos do Azure

Este tutorial presume que você já estabeleceu um compartilhamento de arquivos do Azure. Para fazer o backup do compartilhamento de arquivos do Azure:

1. Crie um cofre de Serviços de Recuperação na mesma região que o compartilhamento de arquivos. Caso já tenha um cofre, abra a página de Visão geral do cofre e clique em **Backup**.

    ![Clique em Backup na página de visão geral do seu cofre](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. No menu **Meta de backup**, em **Do que deseja fazer backup?** , escolha o Compartilhamento de Arquivos do Azure.

    ![Escolha o compartilhamento de arquivos do Azure como meta de Backup](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Clique em **Backup** para configurar o compartilhamento de arquivos do Azure para seu cofre de Serviços de Recuperação.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Depois de o cofre ser associado ao compartilhamento de arquivos do Azure, o menu Backup é aberto e solicita que você selecione uma Conta de armazenamento. O menu exibe todas as Contas de armazenamento com suporte na região do cofre e que ainda não estão associadas a um cofre de Serviços de Recuperação.

   ![Selecione sua conta de armazenamento](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. Na lista de Contas de armazenamento, selecione uma conta e clique em **OK**. Na conta de armazenamento, o Azure procura compartilhamentos de arquivos cujo backup pode ser feito. Caso tenha adicionado seus compartilhamentos de arquivos e não os esteja vendo na lista, aguarde um pouco até que eles sejam exibidos.

   ![Compartilhamentos de arquivos estão sendo detectados](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. Na lista de **Compartilhamentos de Arquivos**, selecione um ou mais dos compartilhamentos de arquivos dos quais deseja fazer backup e clique em **OK**.

6. Depois de escolher seus Compartilhamentos de Arquivos, o menu Backup alterna para **Política de backup**. Nesse menu, selecione uma política de backup existente ou crie uma nova, depois clique em **Habilitar backup**.

   ![Selecione uma política de backup ou crie uma nova](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Depois de estabelecer uma política de backup, um instantâneo dos Compartilhamentos de Arquivos será executado no horário agendado, e o ponto de recuperação fica retido para o período escolhido.

## <a name="create-an-on-demand-backup"></a>Criar um backup sob demanda

Depois de configurar a política de backup, você desejará criar um backup sob demanda para garantir que seus dados estarão protegidos até o próximo backup agendado.

### <a name="to-create-an-on-demand-backup"></a>Para criar um backup sob demanda

1. Abra o cofre de Serviços de Recuperação que contém os pontos de recuperação do compartilhamento de arquivos e clique em **Itens de Backup**. A lista com os tipos de Itens de Backup é exibida.

   ![Lista de itens de backup](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. Na lista, selecione **Armazenamento do Azure (Arquivos do Azure)** . A lista de compartilhamentos de arquivos do Azure é exibida.

   ![Lista de compartilhamentos de arquivos do Azure](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivo desejado. O menu Item de Backup do compartilhamento de arquivo selecionado é aberto.

   ![Menu de item de backup do compartilhamento de arquivo selecionado](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. No menu Item de Backup, clique em **Fazer Backup Agora**. Como esse é um trabalho de backup sob demanda, não há nenhuma política de retenção associada ao ponto de recuperação. A caixa de diálogo **Fazer Backup Agora** é aberta. Especifique até que dia deseja manter o ponto de recuperação escolhendo o último dia.

   ![Escolha a data de retenção do ponto de recuperação](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o portal do Azure para:

> [!div class="checklist"]
>
> * Configurar um cofre de Serviços de Recuperação para fazer backup de Arquivos do Azure
> * Executar um trabalho de backup sob demanda para criar um ponto de restauração

Continue para o próximo artigo a fim de restaurar um backup de um compartilhamento de arquivos do Azure.

> [!div class="nextstepaction"]
> [Restaurar a partir do backup do compartilhamento de arquivos do Azure](restore-afs.md)
