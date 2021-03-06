---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Elium | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791235"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Elium

Neste tutorial, você aprenderá a integrar o Elium ao Azure AD (Azure Active Directory). Quando integrar o Elium ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Elium.
* Permitir que os usuários sejam conectados automaticamente ao Elium com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Elium habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Elium dá suporte ao SSO iniciado por **SP e IDP**
* O Elium é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-elium-from-the-gallery"></a>Adição do Elium a partir da galeria

Para configurar a integração do Elium com o Microsoft Azure Active Directory, você precisa adicionar o Elium da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Elium** na caixa de pesquisa.
1. Escolha **Elium** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Configurar e testar logon único do Azure AD para o Elium

Configure e teste o SSO do Azure AD com o Elium usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Elium.

Para configurar e testar o SSO do Azure AD com o Elium, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Elium](#configure-elium-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Elium](#create-elium-test-user)** – para ter um equivalente de B. Fernandes no Elium que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Elium**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Esses valores não são reais. Você obterá esses valores do **arquivo de metadados de SP** disponível para download em `https://<platform-domain>.elium.com/login/saml2/metadata`, o que é explicado mais adiante neste tutorial.

1. O aplicativo Elium espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Elium espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | NOME | Atributo de Origem|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Essas são as declarações padrão. **Somente a declaração de email é necessária**. Para provisionamento de JIT, apenas a declaração de email será obrigatória também. Outras declarações personalizadas podem variar de uma plataforma de cliente para outra.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Elium**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Elium.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Elium**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-elium-sso"></a>Configurar o SSO do Elium

1. Para automatizar a configuração no Elium, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o Elium** direcionará você ao aplicativo Elium. De lá, forneça as credenciais de administrador para entrar no Elium. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se desejar configurar o Elium manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Elium como administrador e execute as seguintes etapas:

1. Clique no **Perfil de usuário** no canto superior direito e selecione **Administração**.

    ![Configurar o logon único](./media/elium-tutorial/user1.png)

1. Selecione a guia **Segurança**.

    ![Configurar o logon único](./media/elium-tutorial/user2.png)

1. Role até a seção **Logon único (SSO)** e execute as seguintes etapas:

    ![Configurar o logon único](./media/elium-tutorial/user3.png)

    a. Copie o valor de **Verificar se a autenticação SAML2 funciona para sua conta** e cole-o na caixa de texto **URL de logon** na seção **Configuração SAML Básica** no portal do Azure.

    > [!NOTE]
    > Depois de configurar o SSO, você poderá sempre acessar a página de logon remoto padrão na seguinte URL: `https://<platform_domain>/login/regular/login` 

    b. Marque a caixa de seleção **Ativar federação de SAML2**.

    c. Marque a caixa de seleção **Provisionamento de JIT**.

    d. Abra o **SP Metadata** clicando no botão **Fazer o download**.

    e. Procure **entityID** no arquivo **SP Metadata**, copie o valor **entityID** e cole-o na caixa de texto **Identificador** na seção **Configuração SAML Básica** no portal do Azure. 

    ![Configurar o logon único](./media/elium-tutorial/user4.png)

    f. Procure **AssertionConsumerService** no arquivo **SP Metadata**, copie o valor **Localização** e cole-o na caixa de texto **URL de resposta** na seção **Configuração SAML Básica** no portal do Azure.

    ![Configurar o logon único](./media/elium-tutorial/user5.png)

    g. Abra o arquivo de metadados baixado do Portal do Azure no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do IdP**.

    h. Clique em **Save** (Salvar).

### <a name="create-elium-test-user"></a>Criar um usuário de teste do Elium

Nesta seção, um usuário chamado B.Fernandes será criado no Elium. O Elium dá suporte ao **provisionamento Just-In-Time**, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Elium, um novo será criado quando você tentar acessar o Elium.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Elium no Painel de Acesso, você deve ser conectado automaticamente ao Elium para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Elium com o Azure AD](https://aad.portal.azure.com/)