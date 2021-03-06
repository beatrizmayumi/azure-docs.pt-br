---
title: Atualizar ou remover uma atribuição de função personalizada do Azure AD no PIM (Privileged Identity Management) | Microsoft Docs
description: Como atualizar ou remover uma atribuição de função personalizada do Azure AD no PIM (Privileged Identity Management)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace189db569941371026b76c4438515f4c53e77b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896513"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Atualizar ou remover uma função personalizada do Azure AD atribuída no Privileged Identity Management

Este artigo informa como usar o PIM (Privileged Identity Management) para atualizar ou remover a atribuição Just-In-Time e com limite de tempo a funções personalizadas criadas para gerenciamento de aplicativos na experiência administrativa do Azure AD (Azure Active Directory). 

- Para saber mais sobre como criar funções personalizadas para delegar o gerenciamento de aplicativos no Azure AD, confira [Funções Administrador personalizadas no Azure Active Directory (versão prévia)](../users-groups-roles/roles-custom-overview.md). 
- Se você ainda não usou o Privileged Identity Management, obtenha mais informações em [Começar a usar o Privileged Identity Management](pim-getting-started.md).

> [!NOTE]
> As funções personalizadas do Azure AD não são integradas às funções de diretório internas durante a versão prévia. Depois que a funcionalidade estiver em disponibilidade geral, o gerenciamento de função ocorrerá na experiência de funções internas.

## <a name="update-or-remove-an-assignment"></a>Atualizar ou remover uma atribuição

Siga estas etapas para atualizar ou remover uma atribuição de função personalizada existente.

1. Entre no [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal do Azure com uma conta de usuário atribuída à função de administrador de funções com privilégios.
1. Selecione **funções personalizadas do Azure AD (versão prévia)** .

    ![Selecione a versão prévia das funções personalizadas do Azure AD para ver as atribuições de função elegíveis](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **Funções** para ver a lista de **Atribuições** de funções personalizadas para aplicativos do Azure AD.

    ![Selecione Funções para ver a lista e atribuições de função elegíveis](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Selecione a função que você deseja atualizar ou remover.
1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.
1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

    ![Selecione remover ou atualizar na atribuição de função qualificada](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Próximos passos

- [Ativar uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
