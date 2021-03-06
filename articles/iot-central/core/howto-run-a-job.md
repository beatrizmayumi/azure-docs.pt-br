---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem o gerenciamento de dispositivos em massa, como atualizar uma propriedade do dispositivo, configurar ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 68d4dbff364f8d3fda72fc2377722031e9cccc3d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018883"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar um trabalho em seu aplicativo de IoT Central do Azure

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. Os trabalhos permitem que você faça atualizações em massa para comandos e propriedades de dispositivo. Este artigo explica como começar a usar trabalhos em seu próprio aplicativo.


## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Ele mostra como aumentar a velocidade do ventilador para várias máquinas de vendas refrigeradas.

1. Navegue até o painel Trabalhos no painel de navegação.

2. Selecione **+ novo** para criar um novo trabalho.

    ![Criar novo trabalho](./media/howto-run-a-job/createnewjob.png)

3. Insira um nome e uma descrição para identificar o trabalho que você está criando.

4. Selecione o grupo de dispositivos ao qual você deseja que seu trabalho se aplique. Você pode ver quantos dispositivos sua configuração de trabalho será aplicada na seção de resumo. 

5. Em seguida, escolha o tipo de trabalho a ser definido (Propriedade ou comando). Defina a configuração do trabalho selecionando a propriedade e definindo novos valores ou escolha um comando. É possível adicionar várias propriedades de cada vez.

    ![Configurar trabalho](./media/howto-run-a-job/configurejob.png)

6. No lado direito, escolha os dispositivos nos quais você gostaria de executar o trabalho. Ao marcar a caixa de seleção superior, todos os dispositivos são selecionados em todo o conjunto de dispositivos. Ao marcar a caixa de seleção ao lado de **nome**, todos os dispositivos na página atual são selecionados.

7. Depois de selecionar os dispositivos, escolha **executar** ou **salvar**. O trabalho agora aparece na página de **trabalhos** principais. Nessa exibição, você pode ver seu trabalho em execução no momento e o histórico de qualquer trabalho executado anteriormente. O trabalho em execução sempre aparece na parte superior da lista. Seu trabalho salvo pode ser aberto novamente a qualquer momento para continuar editando ou para ser executado.

    ![Exibir trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Você pode exibir o histórico de seus trabalhos executados anteriormente por até 30 dias.

7. Para obter uma visão geral do seu trabalho, selecione o trabalho a ser exibido na lista. Esta visão geral contém os detalhes do trabalho, dispositivos e valores de status do dispositivo. Nesta visão geral, você também pode selecionar **baixar detalhes do trabalho** para baixar um arquivo. csv dos detalhes do seu trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para a solução de problemas.

    ![Exibir status do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Parar um trabalho em execução

Para interromper um trabalho em execução, selecione-o e escolha **parar**. O status do trabalho muda para refletir que o trabalho foi interrompido.

   ![Parar o trabalho](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar um trabalho interrompido

Para executar um trabalho que está parado no momento, selecione o trabalho parado. Escolha **executar** no painel. O status do trabalho muda para refletir que o trabalho agora está em execução novamente.

   ![Trabalho retomado](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um trabalho existente que você criou, abra um trabalho que foi criado e selecione **copiar**. Uma nova cópia da configuração do trabalho é aberta para você editar. Você pode salvar ou executar o novo trabalho. 

   ![Trabalho de cópia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Exibir o status do trabalho

Depois que um trabalho é criado, a coluna **status** é atualizada com a mensagem de status mais recente do trabalho. A tabela a seguir lista os valores de status possíveis:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Com falha               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não começou a ser executado em dispositivos.         |
| Executando              | Esse trabalho está sendo executado nos dispositivos.             |
| Parada              | Este trabalho foi interrompido manualmente por um usuário.           |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os possíveis valores de status do dispositivo:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Teve êxito            | O número de dispositivos em que o trabalho foi executado com êxito.       |
| Com falha               | O número de dispositivos nos quais o trabalho falhou ao ser executado.       |

### <a name="view-the-device-status"></a>Exibir o status do dispositivo

Para exibir o status do trabalho e de todos os dispositivos afetados, selecione o trabalho. Para baixar um arquivo. csv que inclui os detalhes do trabalho, incluindo a lista de dispositivos e seus valores de status, selecione **baixar detalhes do trabalho**. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com falha               | A execução do trabalho falhou neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo tiver sido excluído, você não poderá selecionar o dispositivo e ele será exibido como excluído com a ID do dispositivo.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como criar trabalhos em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-device-template.md)
