---
title: Criar senhas de aplicativo de informações de segurança (visualização) página-Azure AD
description: Crie senhas geradas automaticamente (senhas de aplicativo) para usar com qualquer aplicativo sem navegador ou qualquer aplicativo que não dê suporte à verificação de dois fatores em sua organização. Essa senha de aplicativo é separada da senha normal e pode ser configurada na página de informações de segurança.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.openlocfilehash: d4076e379755518421ef79eca542ebdc99294a11
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704877"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Criar senhas de aplicativo da página informações de segurança (versão prévia)

Alguns aplicativos, como o Outlook 2010, não dão suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas, o aplicativo não funcionará. Para contornar este problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo que não seja o navegador, separado da sua senha normal.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>O administrador pode não permitir que você use senhas de aplicativo. Caso você não veja a opção **Senhas de aplicativos**, elas não estão disponíveis em sua organização.

Ao usar senhas de aplicativos, é importante lembrar:

- As senhas de aplicativo são geradas automaticamente e devem ser criadas e inseridas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar um após esse limite, será solicitado que você exclua uma senha existente antes de poder criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, depois que a verificação em duas etapas estiver ativada, você não precisará mais de senhas de aplicativos para clientes do Office 2013. Para obter mais informações, consulte o artigo [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicativos

Se você usa a verificação em duas etapas com sua conta corporativa ou de estudante e o administrador ativou a experiência de informações de segurança, é possível criar e excluir suas senhas de aplicativos usando a página **Informações de segurança**.

>[!Note]
>Se o administrador não tiver ativado a experiência de informações de segurança, você deverá seguir as instruções e as informações na seção [Gerenciar senhas de aplicativo para verificação em duas etapas](multi-factor-authentication-end-user-app-passwords.md).

### <a name="to-create-a-new-app-password"></a>Criar uma nova senha de aplicativo

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myprofile.microsoft.com/.

    ![Página Meu Perfil, com links para Informações de segurança em destaque](media/security-info/securityinfo-myprofile-page.png)

2. Escolha **Informações de segurança** no painel de navegação esquerdo ou pelo link no bloco **Informações de segurança**. Em seguida, escolha **Adicionar método** na página **Informações de segurança**.

    ![Página Informações de segurança com a opção Adicionar método em destaque](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar método**, selecione **Senha de aplicativo** na lista suspensa e selecione **Adicionar**.

    ![Caixa Adicionar método, com Senha de aplicativo selecionada](media/security-info/securityinfo-myprofile-addpassword.png)

4. Digite o nome do aplicativo que exige a senha de aplicativo e, em seguida, selecione **Avançar**.

    ![Página Senha de aplicativo, com o nome do aplicativo](media/security-info/securityinfo-myprofile-password-appname.png)

5. Copie o texto da caixa **Senha**, cole a senha na área de senha do aplicativo (no exemplo, Outlook 2010) e, em seguida, selecione **Concluído**.

    ![Página Senha de aplicativo, com o nome do aplicativo](media/security-info/securityinfo-myprofile-password-copytext.png)

    A senha é adicionada e você poderá acessar seu aplicativo no futuro.

## <a name="delete-your-app-passwords"></a>Excluir senhas de aplicativos

Se você não precisar mais usar um aplicativo que exija uma senha de aplicativo, poderá excluir a senha de aplicativo associada. Excluir a senha de aplicativo libera um dos pontos de senha de aplicativo disponíveis para uso futuro.

>[!Important]
>Ao excluir uma senha de aplicativo por engano, não será possível desfazer essa ação. Você precisará criar uma nova senha de aplicativo e inseri-la novamente no aplicativo, seguindo as etapas da seção [Criar novas senhas de aplicativo](#create-new-app-passwords) deste artigo.

### <a name="to-delete-an-app-password"></a>Excluir uma senha de aplicativo

1. Na página **Informações de segurança**, selecione o link **Excluir** próximo à opção **Senha de aplicativo** do aplicativo desejado.

    ![Link para excluir o método de senha de aplicativo em informações de segurança](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecione **Sim** na caixa de confirmação para excluir a **Senha de aplicativo**. Depois que a senha de aplicativo for excluída, ela será removida das suas informações de segurança e desaparecerá da página **Informações de segurança**.

## <a name="for-more-information"></a>Para obter mais informações

- Para obter mais informações sobre a página de **Informações de segurança** e como configurá-la, confira [Visão geral das informações de segurança](user-help-security-info-overview.md)
