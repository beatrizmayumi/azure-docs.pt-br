---
title: 'Azure AD Connect: Próximas etapas e como gerenciar o Azure AD Connect | Microsoft Docs'
description: Aprenda a estender configuração padrão e tarefas operacionais para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571334"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Próximas etapas e como gerenciar o Azure AD Connect
Use os procedimentos operacionais neste artigo para personalizar o Azure AD (Azure Active Directory) Connect para atender às necessidades e requisitos de sua organização.  

## <a name="add-additional-sync-admins"></a>Adicionar administradores de sincronização adicionais
Por padrão, somente o usuário que fez a instalação e os administradores locais podem gerenciar o mecanismo de sincronização instalado. Para que outras pessoas possam acessar e gerenciar o mecanismo de sincronização, localize o grupo chamado ADSyncAdmins no servidor local e adicione-os a esse grupo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Atribuir licenças aos usuários do Azure AD Premium e do Enterprise Mobility Suite
Agora que os usuários foram sincronizados para a nuvem, você precisará atribuir uma licença para que eles possam começar a usar aplicativos de nuvem como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Como atribuir uma licença do Enterprise Mobility Suite ou do Azure AD Premium

1. Entre no Portal do Azure como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do **Active Directory**, clique duas vezes no diretório que tem os usuários que você deseja configurar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. Na página **Licenças**, selecione **Active Directory Premium** ou **Enterprise Mobility Suite** e, em seguida, clique em **Atribuir**.
6. Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.

## <a name="verify-the-scheduled-synchronization-task"></a>Verificar a tarefa de sincronização agendada
Use o Portal do Azure para verificar o status de uma sincronização.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Como verificar a tarefa de sincronização agendada
1. Entre no Portal do Azure como um administrador.
2. Selecione **Active Directory**à esquerda.
3. À esquerda, selecione **do Azure AD Connect**
4. Na parte superior da página, observe a última sincronização.

![Hora de sincronização de diretório](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Iniciar uma tarefa de sincronização agendada
Se você precisar executar uma tarefa de sincronização, você pode fazer isso:

1. Clique duas vezes no atalho da área de trabalho do Azure AD Connect para iniciar o assistente.
2. Clique em **Configurar**.
3. Na tela de tarefas, selecione a **personalizar opções de sincronização** e clique em **Avançar**
4. Insira suas credenciais de AD do Azure
5. Clique em **Avançar**. Clique em **Avançar**.  Clique em **Avançar**.
5.  Sobre o **pronto para configurar** tela, certifique-se de que o **iniciar o processo de sincronização quando configuração for concluída** caixa está marcada.
6.  Clique em **Configurar**.

Para obter mais informações sobre o Agendador de sincronização do Azure AD Connect, consulte [Agendador do Azure AD Connect](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, você pode sempre iniciar o assistente novamente de um atalho da área de trabalho ou da página inicial do Azure AD Connect.  Você observará que executar novamente o assistente fornece algumas novas opções na forma de tarefas adicionais.  

A tabela a seguir fornece um resumo dessas tarefas e uma breve descrição de cada uma delas.

![Lista de tarefas adicionais](./media/how-to-connect-post-installation/addtasks2.png)

| Tarefa adicional | DESCRIÇÃO |
| --- | --- |
|**Configurações de privacidade**|Exiba os dados de telemetria que estão sendo compartilhados com a Microsoft.|
|**Exibir a configuração atual**|Exiba sua solução atual do Azure AD Connect.  Isso inclui configurações gerais, diretórios sincronizados e configurações de sincronização. |
| **Personalizar opções de sincronização** |Altere a configuração atual, por exemplo adicionando florestas do Active Directory para a configuração ou habilitando opções de sincronização como usuário, grupo, dispositivo ou write-back de senha. |
|**Configurar opções do dispositivo**|Opções de dispositivo disponíveis para sincronização|
|**Atualizar esquema de diretório**|Permite que você adicione novos objetos de diretório local para sincronização|
|**Configurar o modo de preparo** |Informações de preparo que não são sincronizadas imediatamente e não são exportadas para o Azure AD ou o Active Directory local.  Com esse recurso, você pode visualizar as sincronizações antes que elas ocorram. |
|**Alterar entrada do usuário**|Alterar o método de autenticação que os usuários estão usando para entrar|
|**Gerenciar a federação**|Gerenciar sua infraestrutura do AD FS, renovar os certificados e adicionar servidores do AD FS|
|**Solucionar problemas**|Ajuda com solução de problemas do Azure AD Connect|

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [como integrar suas identidades locais ao Azure Active Directory](whatis-hybrid-identity.md).
