---
title: 'Início Rápido: Configurar as credenciais para um aplicativo da área de trabalho'
titleSuffix: Azure AD B2C
description: Neste Início Rápido, execute um aplicativo de desktop WPF de exemplo que usa o Azure Active Directory B2C para fornecer a entrada na conta.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 01adebbab1e39e2de5603be4c95335a5fe40ef06
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841404"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Início Rápido: configurar a entrada para um aplicativo da área de trabalho usando o Azure Active Directory B2C

O Azure AD B2C (Azure Active Directory B2C) fornece gerenciamento de identidades de nuvem para manter seu aplicativo, sua empresa e seus clientes protegidos. O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais e corporativas usando protocolos padrão. Neste início rápido, você pode usar um aplicativo de área de trabalho WPF (Windows Presentation Foundation) para iniciar sessão usando um provedor de identidade de redes sociais e chamar uma API Web do Azure AD B2C protegida.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento Web e do ASP.NET**.
- Uma conta social do Facebook, do Google ou da Microsoft.
- [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Executar o aplicativo no Visual Studio

1. Na pasta de projeto do aplicativo de exemplo, abra a solução **active-directory-b2c-wpf.sln** no Visual Studio.
2. Pressione **F5** para depurar o aplicativo.

## <a name="sign-in-using-your-account"></a>Iniciar sessão usando sua conta

1. Clique em **Entrar** para iniciar o fluxo de trabalho **Criar conta ou entrar**.

    ![Captura de tela do aplicativo WPF de exemplo](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    O exemplo é compatível com várias opções de inscrição. Essas opções incluem usar um provedor de identidade social ou criar uma conta local usando um endereço de email. Para este guia de início rápido, use uma conta de provedor de identidade social do Facebook, do Google ou da Microsoft.


2. O Azure AD B2C apresenta uma página de entrada para uma empresa fictícia chamada Fabrikam para o aplicativo Web de exemplo. Para inscrever-se usando um provedor de identidade social, clique no botão do provedor de identidade que você deseja usar.

    ![Página de entrada ou inscrição mostrando os provedores de identidade](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Você se autentica (entra) usando as credenciais de sua conta social e autoriza o aplicativo a ler as informações da sua conta social. Ao conceder o acesso, o aplicativo poderá recuperar informações de perfil da conta social, tais como seu nome e cidade.

2. Conclua o processo de entrada para o provedor de identidade.

    Os detalhes do perfil da sua nova conta são populados previamente com as informações da sua conta social.

## <a name="edit-your-profile"></a>Editar o perfil

O Azure AD B2C fornece funcionalidade para permitir que usuários atualizem seus perfis. O aplicativo Web de exemplo usa um fluxo de usuário de perfil de edição do Azure AD B2C no fluxo de trabalho.

1. Na barra de menus do aplicativo, clique em **Editar perfil** para editar o perfil que você criou.

    ![Botão Editar perfil realçado no aplicativo WPF de exemplo](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Escolha o provedor de identidade associado à conta que você criou. Por exemplo, se você tiver usado o Facebook como o provedor de identidade quando criou a conta, escolha o Facebook para modificar os detalhes do perfil associado.

3. Altere seu **Nome de exibição** ou **Cidade** e clique em **Continuar**.

    Um novo token de acesso é exibido na caixa de texto *Informações de token*. Se você quiser verificar as alterações no seu perfil, copie e cole o token de acesso no decodificador de token https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Acessar um recurso de API protegido

Clique em **Chamada à API** para fazer uma solicitação para o recurso protegido.

![Chamar API](./media/quickstart-native-app-desktop/call-api-wpf.png)

O aplicativo inclui o token de acesso do Azure AD na solicitação para o recurso da API Web protegida. A API Web retorna o nome de exibição contido no token de acesso.

Você usou com êxito sua conta de usuário do Azure AD B2C para fazer uma chamada autorizada a uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode usar o locatário do Azure AD B2C se planeja experimentar outros tutoriais ou inícios rápidos do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou um aplicativo de área de trabalho de exemplo para:

* Entrar com uma página de logon personalizada
* Entrar com um provedor de identidade social
* Criar uma conta do Azure AD B2C
* Chamar uma API Web protegida pelo Azure AD B2C

Introdução à criação de seu próprio locatário do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um locatário do Azure Active Directory B2C no Portal do Azure](tutorial-create-tenant.md)
