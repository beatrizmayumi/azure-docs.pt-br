---
title: 'Tutorial: Configurar o Smartfile para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Smartfile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: da7c95461696d3246995b5ad36a906cc3dd909c2
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775442"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Tutorial: Configurar o Smartfile para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Smartfile e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Smartfile.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário](https://www.SmartFile.com/pricing/)do smartfile.
* Uma conta de usuário no Smartfile com permissões de administrador.

## <a name="assigning-users-to-smartfile"></a>Atribuindo usuários ao Smartfile

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Smartfile. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Smartfile seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Dicas importantes para atribuir usuários ao Smartfile

* É recomendável que um único usuário do Azure AD seja atribuído ao Smartfile para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Smartfile, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-smartfile-for-provisioning"></a>Configurar o Smartfile para provisionamento

Antes de configurar o Smartfile para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no Smartfile e coletar detalhes adicionais necessários.

1. Entre no console de administração do Smartfile. Navegue até o canto superior direito do console de administração do Smartfile. Selecione **chave do produto**.

    ![Console de administração do smartfile](media/smartfile-provisioning-tutorial/login.png)

2. Para gerar um token de portador, copie a **chave do produto** e a senha do **produto**. Cole-os em um bloco de notas com dois-pontos entre eles.
    
     ![Adicionar SCIM do smartfile](media/smartfile-provisioning-tutorial/auth.png)

    ![Adicionar SCIM do smartfile](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Adicionar o Smartfile da Galeria

Para configurar o Smartfile para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o Smartfile da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Smartfile da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **smartfile**, selecione **smartfile** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SmartFile na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configurando o provisionamento automático de usuário para o Smartfile 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Smartfile com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Smartfile, seguindo as instruções fornecidas no [tutorial de logon único](SmartFile-tutorial.md)do smartfile. O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Smartfile no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SmartFile**.

    ![O link do SmartFile na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5.  Na seção **credenciais de administrador** , insira `https://<SmartFile sitename>.smartfile.com/ftp/scim` a **URL de locatário**. Um exemplo seria semelhante `https://demo1test.smartfile.com/ftp/scim`. Insira o valor do **token** de portador (ProductKey: ProductPassword) que você recuperou anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao smartfile. Se a conexão falhar, verifique se sua conta do Smartfile tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de Notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para**o smartfile.

    ![Mapeamentos de usuário do smartfile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Smartfile na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no smartfile para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do smartfile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para**o smartfile.

    ![Mapeamentos de grupos do smartfile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Smartfile na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no smartfile para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo do smartfile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Smartfile, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Smartfile escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no smartfile.

    Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitações do conector

* O smartfile dá suporte apenas a exclusões físicas. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

 [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
