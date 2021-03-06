---
title: Visão geral de tokens-Azure Active Directory B2C
description: Saiba mais sobre os tokens usados em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 543a3558333933e9d8d6262c76c1e6e9419be877
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848181"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Visão geral dos tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada [fluxo de autenticação](application-types.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## <a name="token-types"></a>Tipos de token

O Azure AD B2C dá suporte aos [protocolos OAuth 2,0 e OpenID Connect](protocols-overview.md), que usa tokens para autenticação e acesso seguro aos recursos. Todos os tokens usados em Azure AD B2C são [tokens da Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm asserções de informações sobre o portador e o assunto do token.

Os tokens a seguir são usados na comunicação com o Azure AD B2C:

- *Token de ID* -um JWT que contém declarações que você pode usar para identificar usuários em seu aplicativo. Esse token é enviado com segurança em solicitações HTTP para comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um token de ID como desejar. Normalmente, eles são usados para exibir informações da conta ou para tomar decisões de controle de acesso em um aplicativo. Os tokens de ID são assinados, mas não são criptografados. Quando seu aplicativo ou API recebe um token de ID, ele deve validar a assinatura para provar que o token é autêntico. Seu aplicativo ou API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas validações de declaração comuns em cada cenário.
- *Token de acesso* -um JWT que contém declarações que você pode usar para identificar as permissões concedidas para suas APIs. Tokens de acesso são assinados, mas não são criptografados. Tokens de acesso são usados para fornecer acesso a APIs e servidores de recursos.  Quando a API recebe um token de acesso, ela deve validar a assinatura para provar que o token é autêntico. Sua API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas validações de declaração comuns em cada cenário.
- *Tokens de atualização –* tokens de atualização são usados para adquirir novos tokens de ID e tokens de acesso em um fluxo OAuth 2,0. Eles fornecem ao seu aplicativo um acesso de longo prazo aos recursos em nome dos usuários sem a necessidade de interação com esses usuários. Os tokens de atualização são opacos para seu aplicativo. Eles são emitidos por Azure AD B2C e podem ser inspecionados e interpretados somente pelo Azure AD B2C. Eles são de longa duração, mas seu aplicativo não deve ser escrito com a expectativa de que um token de atualização durará por um período de tempo específico. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de seu aplicativo saber se um token de atualização é válido é tentar resgatá-lo fazendo uma solicitação de token para Azure AD B2C. Quando você resgatar um token de atualização para um novo token, receberá um novo token de atualização na resposta do token. Salve o novo token de atualização. Ele substitui o token de atualização que você usou anteriormente na solicitação. Essa ação ajuda a garantir que seus tokens de atualização permaneçam válidos pelo máximo de tempo possível.

## <a name="endpoints"></a>Pontos de Extremidade

Um [aplicativo registrado](tutorial-register-applications.md) recebe tokens e se comunica com Azure ad B2C enviando solicitações para esses pontos de extremidade:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Os tokens de segurança que seu aplicativo recebe do Azure AD B2C podem vir dos pontos de extremidade `/authorize` ou `/token`. Quando os tokens de ID são adquiridos do ponto de extremidade `/authorize`, ele é feito usando o [fluxo implícito](implicit-flow-single-page-application.md), que geralmente é usado para usuários que entram em aplicativos Web baseados em JavaScript. Quando os tokens de ID são adquiridos do ponto de extremidade `/token`, ele é feito usando o [fluxo do código de autorização](openid-connect.md#get-a-token), o que mantém o token oculto do navegador.

## <a name="claims"></a>Declarações

Ao usar o Azure AD B2C, você tem um controle refinado sobre o conteúdo de seus tokens. Você pode configurar [fluxos de usuário](user-flow-overview.md) e [políticas personalizadas](custom-policy-overview.md) para enviar determinados conjuntos de dados de usuário em declarações que são necessárias para seu aplicativo. Essas declarações podem incluir propriedades padrão como **DisplayName** e **EmailAddress**. Seus aplicativos podem usar essas declarações para autenticar usuários e solicitações com segurança.

As declarações em tokens de ID não são retornadas em nenhuma ordem específica. Novas declarações podem ser introduzidas em tokens de ID a qualquer momento. Seu aplicativo não deve ser interrompido à medida que novas declarações são introduzidas. Você também pode incluir [atributos de usuário personalizados](user-flow-custom-attributes.md) em suas declarações.

A tabela a seguir lista as declarações que você pode esperar em tokens de ID e tokens de acesso emitidos por Azure AD B2C.

| Nome | Declaração | Valor de exemplo | Description |
| ---- | ----- | ------------- | ----------- |
| Público | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do token. Por Azure AD B2C, o público-alvo é a ID do aplicativo. Seu aplicativo deve validar esse valor e rejeitar o token se ele não corresponder. Público-alvo é sinônimo de recurso. |
| Emissor | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o STS (serviço de token de segurança) que constrói e retorna o token. Ele também identifica o diretório no qual o usuário foi autenticado. Seu aplicativo deve validar a declaração do emissor para certificar-se de que o token veio do ponto de extremidade apropriado. |
| Emitido em | `iat` | `1438535543` | A hora em que o token foi emitido, representada na época. |
| Hora de expiração | `exp` | `1438539443` | A hora em que o token se torna inválido, representada na época. Seu aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Não antes de | `nbf` | `1438535543` | O horário em que o token se torna inválido, representado no horário da época. Esse tempo geralmente é igual ao horário em que o token foi emitido. Seu aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Versão | `ver` | `1.0` | A versão do token de ID, conforme definido por Azure AD B2C. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código incluído em um token de ID somente quando o token é emitido junto com um código de autorização OAuth 2,0. Um hash de código pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como executar essa validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash de token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de token de acesso incluído em um token de ID somente quando o token é emitido junto com um token de acesso OAuth 2,0. Um hash de token de acesso pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como executar essa validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia usada para reduzir ataques de reprodução de token. Seu aplicativo pode especificar um nonce em uma solicitação de autorização usando o parâmetro de consulta `nonce`. O valor que você fornece na solicitação é emitido sem modificações na declaração de `nonce` apenas de um token de ID. Essa declaração permite que seu aplicativo Verifique o valor em relação ao valor especificado na solicitação. Seu aplicativo deve executar essa validação durante o processo de validação do token de ID. |
| Assunto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | A entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Usado somente com políticas mais antigas. |
| Política de estrutura confiável | `tfp` | `b2c_1_signupsignin1` | O nome da política que foi usada para adquirir o token de ID. |
| Hora da autenticação | `auth_time` | `1438535543` | A hora em que um usuário inseriu as credenciais pela última vez, representada no tempo de época. Não há nenhuma discriminação entre essa autenticação uma entrada nova, uma sessão de logon único (SSO) ou outro tipo de entrada. A `auth_time` é a última vez que o aplicativo (ou usuário) iniciou uma tentativa de autenticação em relação a Azure AD B2C. O método usado para autenticar não é diferenciado. |
| Escopo | `scp` | `Read`| As permissões concedidas ao recurso para um token de acesso. Várias permissões concedidas são separadas por um espaço. |
| Entidade Autorizada | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A **ID do aplicativo** do aplicativo cliente que iniciou a solicitação. |

## <a name="configuration"></a>Configuração

As propriedades a seguir são usadas para [gerenciar os tempos de vida de tokens de segurança](configure-tokens.md) emitidos pelo Azure ad B2C:

- **Duração dos tokens de acesso e ID (minutos)** : o tempo de vida do token portador do OAuth 2.0 usado para obter acesso a um recurso protegido. O padrão é de 60 minutos. O mínimo (inclusivo) é de 5 minutos. O máximo (inclusivo) é de 1440 minutos.

- Tempo de **vida do token de atualização (dias)** – o período máximo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso ou de ID. O período de tempo também abrange a aquisição de um novo token de atualização se o seu aplicativo tiver recebido o escopo de `offline_access`. O padrão é 14 dias. O mínimo (inclusivo) é de um dia. O máximo (inclusivo) é de 90 dias.

- Tempo de **vida da janela deslizante do token de atualização (dias)** – após esse período, o usuário é forçado a reautenticar, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Ele só poderá ser fornecido se a opção estiver definida como **Bounded**. Ele precisa ser maior ou igual ao valor **Vida útil do token de atualização (dias)** . Se a opção estiver definida como **Unbounded**, você não poderá fornecer um valor específico. O padrão é 90 dias. O mínimo (inclusivo) é de um dia. O máximo (inclusivo) é de 365 dias.

Os casos de uso a seguir são ativados usando estas propriedades:

- Permitir que um usuário permaneça conectado a um aplicativo móvel indefinidamente, contanto que o usuário esteja continuamente ativo no aplicativo. Você pode definir **Atualizar duração da janela deslizante do token (dias)** para **Não consolidado** em seu fluxo de usuário.
- Atenda aos requisitos de conformidade e segurança de seu setor definindo a vida de tokens de acesso adequadamente.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

## <a name="compatibility"></a>Compatibilidade

As propriedades a seguir são usadas para [gerenciar a compatibilidade de tokens](configure-tokens.md):

- **Emissor (iss) reivindicação** - essa propriedade identifica o locatário do Azure AD B2C que emitiu o token. O valor padrão é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor de `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inclui IDs para o locatário Azure AD B2C e o fluxo de usuário que foi usado na solicitação de token. Se seu aplicativo ou biblioteca precisar de Azure AD B2C estar em conformidade com a [especificação do OpenID Connect Discovery 1,0](https://openid.net/specs/openid-connect-discovery-1_0.html), use esse valor.

- **Subject (sub) declaração** - Esta propriedade identifica a entidade para a qual o token afirma informações. O valor padrão é **ObjectID**, que popula a declaração de `sub` no token com a ID de objeto do usuário. O valor de **sem suporte** é fornecido apenas para compatibilidade com versões anteriores. É recomendável que você alterne para **ObjectID** assim que for possível.

- **Declaração que representa a ID da política** – essa propriedade identifica o tipo de declaração no qual o nome da política usado na solicitação de token é populado. O valor padrão é `tfp`. O valor de `acr` é fornecido somente para compatibilidade com versões anteriores.

## <a name="pass-through"></a>Passagem

Quando um percurso do usuário é iniciado, Azure AD B2C recebe um token de acesso de um provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Você [habilita uma declaração em seu fluxo de usuário](idp-pass-through-user-flow.md) ou [define uma declaração em sua política personalizada](idp-pass-through-custom.md) para passar o token para os aplicativos que você registra no Azure ad B2C. Seu aplicativo deve usar um [fluxo de usuário v2](user-flow-versions.md) para aproveitar a passagem do token como uma declaração.

Atualmente, Azure AD B2C só dá suporte à passagem do token de acesso dos provedores de identidade OAuth 2,0, que incluem o Facebook e o Google. Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="validation"></a>Validação

Para validar um token, seu aplicativo deve verificar a assinatura e as declarações do token. Muitas bibliotecas de software livre estão disponíveis para validar JWTs, dependendo da linguagem preferida. É recomendável que você explore essas opções em vez de implementar sua própria lógica de validação.

### <a name="validate-signature"></a>Validar assinatura

Um JWT contém três segmentos, um *cabeçalho*, um *corpo*e uma *assinatura*. O segmento de assinatura pode ser usado para validar a autenticidade do token para que ele possa ser confiável pelo seu aplicativo. Os tokens do Azure AD B2C são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256.

O cabeçalho do token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor da Declaração **alg** é o algoritmo que foi usado para assinar o token. O valor da Declaração **Kid** é a chave pública que foi usada para assinar o token. A qualquer momento, Azure AD B2C pode assinar um token usando qualquer um de um conjunto de pares de chaves públicas-privadas. Azure AD B2C gira o possível conjunto de chaves periodicamente. Seu aplicativo deve ser escrito para tratar essas alterações de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD B2C é a cada 24 horas.

O Azure AD B2C tem um ponto de extremidade de metadados OpenID Connect. Usando esse ponto de extremidade, os aplicativos podem solicitar informações sobre Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Seu locatário Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias informações úteis. Os metadados contêm **jwks_uri**, que fornece o local do conjunto de chaves públicas que são usadas para assinar tokens. Esse local é fornecido aqui, mas é melhor buscar o local dinamicamente usando o documento de metadados e analisando **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
O documento JSON localizado nessa URL contém todas as informações de chave pública em uso em um momento específico. O aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar a chave pública no documento JSON que é usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

O documento de metadados para a política de `B2C_1_signupsignin1` no locatário `contoso.onmicrosoft.com` está localizado em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Para determinar qual política foi usada para assinar um token (e onde ir para solicitar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no token. Você pode analisar as declarações fora do corpo do JWT decodificando em base 64 o corpo e desserializando a cadeia de caracteres JSON resultante. A declaração de `acr` é o nome da política que foi usada para emitir o token. A outra opção é codificar a política no valor do parâmetro `state` ao emitir a solicitação e, em seguida, decodificá-la para determinar qual política foi usada. Ambos os métodos são válidos.

Uma descrição de como executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de código-fonte aberto estão disponíveis para ajudá-lo a validar um token.

### <a name="validate-claims"></a>Validar declarações

Quando seus aplicativos ou API recebem um token de ID, ele também deve executar várias verificações em relação às declarações no token de ID. As declarações a seguir devem ser verificadas:

- **público** -verifica se o token de ID foi projetado para ser fornecido ao seu aplicativo.
- **não antes** e **hora de expiração** -verifica se o token de ID não expirou.
- **emissor** -verifica se o token foi emitido para seu aplicativo por Azure ad B2C.
- **nonce** -uma estratégia para mitigação de ataque de reprodução de token.

Para obter uma lista completa de validações que seu aplicativo deve executar, consulte a [especificação do OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [usar tokens de acesso](access-tokens.md).

