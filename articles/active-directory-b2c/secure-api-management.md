---
title: Proteger uma API de gerenciamento de API do Azure usando Azure Active Directory B2C
description: Saiba como usar tokens de acesso emitidos pelo Azure Active Directory B2C para proteger um ponto de extremidade da API de gerenciamento de API do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 942c565c885d59a14d64e7ec06beee0354e7c4ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641633"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Proteger uma API de gerenciamento de API do Azure com Azure AD B2C

Saiba como restringir o acesso à sua API do APIM (gerenciamento de API do Azure) para clientes que se autenticaram com o Azure Active Directory B2C (Azure AD B2C). Siga as etapas neste artigo para criar e testar uma política de entrada no APIM que restringe o acesso apenas às solicitações que incluem um token de acesso válido emitido por Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos em vigor antes de continuar com as etapas neste artigo:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário
* [API publicada](../api-management/import-and-publish.md) no gerenciamento de API do Azure
* [Postmaster](https://www.getpostman.com/) para testar o acesso protegido (opcional)

## <a name="get-azure-ad-b2c-application-id"></a>Obter Azure AD B2C ID do aplicativo

Quando você protege uma API no gerenciamento de API do Azure com Azure AD B2C, você precisa de vários valores para a [política de entrada](../api-management/api-management-howto-policies.md) que você cria em APIM. Primeiro, registre a ID do aplicativo de um aplicativo que você criou anteriormente em seu locatário Azure AD B2C. Se você estiver usando o aplicativo criado nos pré-requisitos, use a ID do aplicativo para *webbapp1*.

Você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada de **registros de aplicativo (versão prévia)** para obter a ID do aplicativo. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Em **gerenciar**, selecione **aplicativos**.
1. Registre o valor na coluna **ID do aplicativo** para *webapp1* ou outro aplicativo que você criou anteriormente.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione a guia **aplicativos pertencentes** .
1. Registre o valor na coluna **ID do aplicativo (cliente)** para *webapp1* ou outro aplicativo que você criou anteriormente.

* * *

## <a name="get-token-issuer-endpoint"></a>Obter ponto de extremidade do emissor do token

Em seguida, obtenha a URL de configuração conhecida para um dos seus Azure AD B2C fluxos de usuário. Você também precisa do URI do ponto de extremidade do emissor do token para o qual você deseja dar suporte no gerenciamento de API do Azure.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **políticas**, selecione **fluxos de usuário (políticas)** .
1. Selecione uma política existente, por exemplo *B2C_1_signupsignin1*, em seguida, selecione **executar fluxo de usuário**.
1. Registre a URL no hiperlink exibido no cabeçalho **executar fluxo de usuário** próximo à parte superior da página. Essa URL é o ponto de extremidade de descoberta conhecido do OpenID Connect para o fluxo do usuário e você o usa na próxima seção ao configurar a política de entrada no gerenciamento de API do Azure.

    ![Hiperlink de URI conhecido na página executar agora do portal do Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecione o hiperlink para navegar até a página de configuração bem-conhecida do OpenID Connect.
1. Na página que é aberta no navegador, registre o valor `issuer`, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Você usará esse valor na próxima seção quando configurar sua API no gerenciamento de API do Azure.

Agora você deve ter duas URLs registradas para uso na próxima seção: a URL do ponto de extremidade de configuração bem conhecida do OpenID Connect e o URI do emissor. Por exemplo:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configurar a política de entrada no gerenciamento de API do Azure

Agora você está pronto para adicionar a política de entrada no gerenciamento de API do Azure que valida chamadas de API. Ao adicionar uma política de [validação de JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) que verifica o público e o emissor em um token de acesso, você pode garantir que somente as chamadas à API com um token válido sejam aceitas.

1. Navegue até sua instância de Gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **APIs**.
1. Selecione a API que você deseja proteger com Azure AD B2C.
1. Selecione a guia **Design**.
1. Em **processamento de entrada**, selecione **\</\>** para abrir o editor de código de política.
1. Coloque a marca de `<validate-jwt>` a seguir dentro da política de `<inbound>`.

    1. Atualize o valor `url` no elemento `<openid-config>` com a URL de configuração conhecida de sua política.
    1. Atualize o elemento `<audience>` com a ID do aplicativo que você criou anteriormente em seu locatário B2C (por exemplo, *webapp1*).
    1. Atualize o elemento `<issuer>` com o ponto de extremidade do emissor do token que você registrou anteriormente.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Validar o acesso à API segura

Para garantir que somente chamadores autenticados possam acessar sua API, você pode validar sua configuração de gerenciamento de API do Azure chamando a API com o [postmaster](https://www.getpostman.com/).

Para chamar a API, você precisa de um token de acesso emitido por Azure AD B2C e de uma chave de assinatura APIM.

### <a name="get-an-access-token"></a>Obter um token de acesso

Primeiro, você precisa de um token emitido por Azure AD B2C para usar no cabeçalho `Authorization` no postmaster. Você pode obter uma usando o recurso **executar agora** do seu fluxo de usuário de inscrição/entrada que você deve ter criado como um dos pré-requisitos.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **políticas**, selecione **fluxos de usuário (políticas)** .
1. Selecione um fluxo de usuário de inscrição/entrada existente, por exemplo *B2C_1_signupsignin1*.
1. Para **aplicativo**, selecione *webapp1*.
1. Para **URL de resposta**, escolha `https://jwt.ms`.
1. Selecione **Executar fluxo de usuário**.

    ![Executar a página de fluxo de usuário para entrar no fluxo de usuário de entrada no portal do Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Conclua o processo de conexão. Você deve ser redirecionado para `https://jwt.ms`.
1. Registre o valor de token codificado exibido no navegador. Você usa esse valor de token para o cabeçalho de autorização no postmaster.

    ![Valor de token codificado exibido em jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Obter chave de assinatura de API

Um aplicativo cliente (neste caso, o postmaster) que chama uma API publicada deve incluir uma chave de assinatura de gerenciamento de API válida em suas solicitações HTTP para a API. Para obter uma chave de assinatura para incluir em sua solicitação HTTP do postmaster:

1. Navegue até a instância do serviço de gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **Assinaturas**.
1. Selecione as reticências do **produto: ilimitado**e selecione **Mostrar/ocultar chaves**.
1. Registre a **chave primária** do produto. Use essa chave para o cabeçalho de `Ocp-Apim-Subscription-Key` em sua solicitação HTTP no postmaster.

![Página chave de assinatura com as chaves mostrar/ocultar selecionadas em portal do Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testar uma chamada à API segura

Com o token de acesso e a chave de assinatura APIM registrada, agora você está pronto para testar se você configurou corretamente o acesso seguro à API.

1. Crie uma nova solicitação de `GET` no [postmaster](https://www.getpostman.com/). Para a URL de solicitação, especifique o ponto de extremidade da lista de alto-falantes da API que você publicou como um dos pré-requisitos. Por exemplo:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Em seguida, adicione os seguintes cabeçalhos:

    | Chave | Valor |
    | --- | ----- |
    | `Authorization` | Valor de token codificado que você registrou anteriormente, prefixado com `Bearer ` (inclua o espaço após "portador") |
    | `Ocp-Apim-Subscription-Key` | Chave de assinatura do APIM que você registrou anteriormente |

    Sua URL de solicitação **Get** e os **cabeçalhos** devem ser semelhantes a:

    ![Interface do usuário do postmaster mostrando a URL de solicitação GET e os cabeçalhos](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecione o botão **Enviar** no postmaster para executar a solicitação. Se você tiver configurado tudo corretamente, deverá receber uma resposta JSON com uma coleção de alto-falantes de conferência (mostrados aqui truncados):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testar uma chamada à API não segura

Agora que você fez uma solicitação bem-sucedida, teste o caso de falha para garantir que as chamadas para sua API com um token *inválido* sejam rejeitadas conforme o esperado. Uma maneira de executar o teste é adicionar ou alterar alguns caracteres no valor do token e, em seguida, executar a mesma `GET` solicitação como antes.

1. Adicione vários caracteres ao valor do token para simular um token inválido. Por exemplo, adicione "INVALID" ao valor do token:

    ![Seção de cabeçalhos da interface do usuário do postmaster mostrando o inválido adicionado ao token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecione o botão **Enviar** para executar a solicitação. Com um token inválido, o resultado esperado é um código de status `401` não autorizado:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se você vir o código de status `401`, verificou que somente os chamadores com um token de acesso válido emitido pelo Azure AD B2C podem fazer solicitações bem-sucedidas para sua API de gerenciamento de API do Azure.

## <a name="support-multiple-applications-and-issuers"></a>Suporte a vários aplicativos e emissores

Normalmente, vários aplicativos interagem com uma única API REST. Para habilitar sua API para aceitar tokens destinados a vários aplicativos, adicione suas IDs de aplicativo ao elemento `<audiences>` na política de entrada APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Da mesma forma, para dar suporte a vários emissores de token, adicione seus URIs de ponto de extremidade ao elemento `<issuers>` na política de entrada APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrar para o b2clogin.com

Se você tiver uma API APIM que valida tokens emitidos pelo ponto de extremidade herdado `login.microsoftonline.com`, você deve migrar a API e os aplicativos que o chamam para usar tokens emitidos pelo [b2clogin.com](b2clogin.md).

Você pode seguir este processo geral para executar uma migração em etapas:

1. Adicione suporte em sua política de entrada do APIM para tokens emitidos por b2clogin.com e login.microsoftonline.com.
1. Atualize seus aplicativos um de cada vez para obter tokens do ponto de extremidade b2clogin.com.
1. Depois que todos os seus aplicativos estiverem obtendo corretamente tokens do b2clogin.com, remova o suporte para tokens emitidos por login.microsoftonline.com da API.

O exemplo de política de entrada APIM a seguir ilustra como aceitar tokens emitidos por b2clogin.com e login.microsoftonline.com. Além disso, ele dá suporte a solicitações de API de dois aplicativos.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes adicionais sobre as políticas de gerenciamento de API do Azure, consulte o [índice de referência da política APIM](../api-management/api-management-policies.md).

Você pode encontrar informações sobre como migrar APIs da Web baseadas em OWIN e seus aplicativos para b2clogin.com em [migrar uma API Web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).
