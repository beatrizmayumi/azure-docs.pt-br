---
title: Selecione um layout de página-Azure Active Directory B2C
description: Saiba mais sobre como selecionar um layout de página em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 452687f3886a85bea796e3899410667ee1d592fa
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840308"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Selecionar um layout de página em Azure Active Directory B2C usando políticas personalizadas

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Você pode habilitar o código do lado do cliente JavaScript em suas políticas de Azure Active Directory B2C (Azure AD B2C) se estiver usando fluxos de usuário ou políticas personalizadas. Para habilitar o JavaScript para seus aplicativos, você deve adicionar um elemento à sua [política personalizada](custom-policy-overview.md), selecionar um layout de página e usar [b2clogin.com](b2clogin.md) em suas solicitações.

Um layout de página é uma associação de elementos que Azure AD B2C fornece e o conteúdo que você fornece.

Este artigo discute como selecionar um layout de página em Azure AD B2C Configurando-o em uma política personalizada.

> [!NOTE]
> Se você quiser habilitar o JavaScript para fluxos de usuário, consulte [JavaScript e versões de layout de página em Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Substituir os valores de DataUri

Em suas políticas personalizadas, você pode ter [ContentDefinitions](contentdefinitions.md) que definem os modelos HTML usados no percurso do usuário. O **ContentDefinition** contém um **DataUri** que se refere aos elementos da página fornecidos pelo Azure AD B2C. O **LoadUri** é o caminho relativo para o conteúdo HTML e CSS que você fornece.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Para selecionar um layout de página, você altera os valores de **DataUri** em seu [ContentDefinitions](contentdefinitions.md) em suas políticas. Ao mudar dos antigos valores de **DataUri** para os novos, você está selecionando um pacote imutável. O benefício de usar esse pacote é que você sabe que ele não será alterado e causará um comportamento inesperado na página.

Para especificar um layout de página em suas políticas personalizadas que usam um valor antigo de **DataUri** , insira `contract` entre `elements` e o tipo de página (por exemplo, `selfasserted`) e especifique o número de versão. Por exemplo:

| Valor antigo de DataUri | Novo valor de DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Log de alterações de versão

Os pacotes de layout de página são atualizados periodicamente para incluir correções e aprimoramentos em seus elementos de página. O log de alterações a seguir especifica as alterações introduzidas em cada versão.

### <a name="200"></a>2.0.0

- Página autodeclarada (`selfasserted`)
  - Adicionado suporte para [controles de exibição](display-controls.md) em políticas personalizadas.

### <a name="120"></a>1.2.0

- Todas as páginas
  - Correções de acessibilidade
  - Agora você pode adicionar o atributo `data-preload="true"` em suas marcas HTML para controlar a ordem de carregamento para CSS e JavaScript. Os cenários incluem:
    - Use isso em seu link do CSS para carregar o CSS ao mesmo tempo que o HTML para que ele não fique piscando entre o carregamento dos arquivos
    - Esse atributo permite que você controle a ordem na qual as marcas de script são buscadas e executadas antes do carregamento da página
  - O campo de email agora é `type=email` e teclados móveis fornecerão as sugestões corretas
  - Suporte para tradução do Chrome
- Página unificada e autodeclarada
  - Os campos nome de usuário/email e senha agora usam o elemento HTML do formulário.  Agora, isso permitirá que o Edge e o IE salvem essas informações corretamente

### <a name="110"></a>1.1.0

- Página de exceção (globalexception)
  - Correção de acessibilidade
  - Removeu a mensagem padrão quando não há contato da política
  - CSS padrão removido
- Página MFA (multifator)
  - Botão ' confirmar código ' removido
  - O campo de entrada para o código agora só usa a entrada de até seis (6) caracteres
  - A página tentará automaticamente verificar o código inserido quando um código de seis dígitos for inserido, sem qualquer botão que precise ser clicado
  - Se o código estiver errado, o campo de entrada será limpo automaticamente
  - Depois de três (3) tentativas com um código incorreto, o B2C envia um erro de volta para a terceira parte confiável
  - Correções de acessibilidade
  - CSS padrão removido
- Página autodeclarada (selfasserted)
  - Removido o alerta de cancelamento
  - Classe CSS para elementos error
  - Mostrar/ocultar a lógica de erro aprimorada
  - CSS padrão removido
- SSP unificado (unifiedssp)
  - Controle KMSI (Mantenha-me conectado) adicionado

### <a name="100"></a>1.0.0

- Versão inicial

## <a name="next-steps"></a>Próximos passos

Encontre mais informações sobre como personalizar a interface do usuário dos seus aplicativos em [Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C](custom-policy-ui-customization.md).
