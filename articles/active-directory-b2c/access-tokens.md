---
title: Solicitar um token de acesso-Azure Active Directory B2C | Microsoft Docs
description: Saiba como solicitar um token de acesso de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d808bca17012d46d1c32658ec0069ca2e2e6a3ac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962026"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Solicitar um token de acesso no Azure Active Directory B2C

Um *token de acesso* contém declarações que você pode usar em Azure Active Directory B2C (Azure ad B2C) para identificar as permissões concedidas para suas APIs. Ao chamar um servidor de recursos, um token de acesso deve estar presente na solicitação HTTP. Um token de acesso é indicado como **access_token** nas respostas de Azure ad B2C.

Este artigo mostra como solicitar um token de acesso para um aplicativo Web e uma API Web. Para obter mais informações sobre tokens no Azure AD B2C, consulte a [visão geral de tokens em Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Não há suporte a cadeias de API Web (em nome de) no Azure AD B2C.** -Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, protegida por Azure AD B2C. Esse cenário é comum em clientes que têm um back-end de API Web que, por sua vez, chama um outro serviço. Este cenário de API Web encadeada pode ter suporte usando a concessão de Credencial de Portador JWT do OAuth 2.0, também conhecida como fluxo Em nome de. No entanto, o fluxo Em Nome de não está implementado atualmente no Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.
- Se você ainda não tiver feito isso, [adicione um aplicativo de API Web ao seu locatário Azure Active Directory B2C](add-web-application.md).

## <a name="scopes"></a>Escopos

Os escopos fornecem uma maneira de gerenciar permissões para recursos protegidos. Quando um token de acesso é solicitado, o aplicativo cliente precisa especificar as permissões desejadas no parâmetro de **escopo** da solicitação. Por exemplo, para especificar o **valor de escopo** de `read` para a API que tem o **URI de ID de aplicativo** de `https://contoso.onmicrosoft.com/api`, o escopo seria `https://contoso.onmicrosoft.com/api/read`.

Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Para adquirir várias permissões na mesma solicitação, você pode adicionar várias entradas no parâmetro de **escopo** único da solicitação, separados por espaços.

O exemplo a seguir mostra os escopos decodificados em uma URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

O exemplo a seguir mostra os escopos codificados em uma URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se você solicitar mais escopos do que o permitido para seu aplicativo cliente, a chamada terá sucesso se pelo menos uma permissão for concedida. A Declaração **SCP** no token de acesso resultante é populada apenas com as permissões que foram concedidas com êxito. O padrão OpenID Connect especifica vários valores de escopo especiais. Os seguintes escopos representam a permissão para acessar o perfil do usuário:

- **OpenID** -solicita um token de ID.
- **offline_access** -solicita um token de atualização usando [fluxos de código de autenticação](authorization-code-flow.md).

Se o parâmetro **response_type** em uma solicitação de `/authorize` incluir `token`, o parâmetro de **escopo** deverá incluir pelo menos um escopo de recurso diferente de `openid` e `offline_access` que será concedido. Caso contrário, a solicitação de `/authorize` falhará.

## <a name="request-a-token"></a>Solicitar um token

Para solicitar um token de acesso, você precisa de um código de autorização. Abaixo está um exemplo de uma solicitação para o ponto de extremidade de `/authorize` para um código de autorização. Não há suporte para domínios personalizados para uso com tokens de acesso. Use seu domínio tenant-name.onmicrosoft.com na URL de solicitação.

Substitua esses valores no exemplo a seguir:

- `<tenant-name>` – O nome de seu locatário do Azure AD B2C.
- `<policy-name>` – O nome da sua política personalizada ou o fluxo de usuário.
- `<application-ID>`-o identificador de aplicativo do aplicativo Web que você registrou para dar suporte ao fluxo do usuário.
- `<redirect-uri>` – O **URI de redirecionamento** que você inseriu ao registrar o aplicativo cliente.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

A resposta com o código de autorização deve ser semelhante a este exemplo:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Depois de receber o código de autorização com êxito, você pode usá-lo para solicitar um token de acesso:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Você verá algo semelhante à seguinte resposta:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Ao usar https://jwt.ms para examinar o token de acesso que foi retornado, você verá algo semelhante ao exemplo a seguir:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como [Configurar tokens no Azure ad B2C](configure-tokens.md)
