---
title: O que é Configuração de Aplicativo do Azure?
description: Uma visão geral do serviço Configuração de Aplicativo do Azure.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/24/2019
ms.openlocfilehash: 40630bbbbcea344fb74d8ad971eb4c808bf0c142
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495240"
---
# <a name="what-is-azure-app-configuration"></a>O que é Configuração de Aplicativo do Azure?

A Configuração de Aplicativos do Azure fornece um serviço para gerenciar centralmente as configurações de aplicativo e os sinalizadores de recurso. Programas modernos, especialmente programas executando em uma nuvem, geralmente possuem muitos componentes que são distribuídos por natureza. A distribuição das definições de configuração entre esses componentes pode levar a erros difíceis de serem resolvidos durante uma implantação de aplicativo. Use a Configuração de Aplicativo para armazenar todas as configurações do aplicativo e proteger os acessos em um só lugar.

Atualmente, a Configuração de Aplicativos está em versão prévia pública. Seu uso é gratuito durante o período de versão prévia. Inscreva-se para usá-la no [portal do Azure](https://portal.azure.com).

## <a name="why-use-app-configuration"></a>Por que usar a Configuração de Aplicativo?

Os aplicativos baseados em nuvem geralmente são executados em várias máquinas virtuais ou contêineres em várias regiões e usam diversos serviços externos. Criar um aplicativo distribuído desse tipo, robusto e escalonável, é um desafio.

Várias metodologias de programação ajudam os desenvolvedores a lidar com a crescente complexidade da criação de aplicativos. Por exemplo, o [Aplicativo de 12 fatores](https://12factor.net/) descreve muitos padrões de arquitetura bem testados e práticas recomendadas para uso com aplicativos em nuvem. Uma recomendação básica deste guia é separar a configuração do código. Nesse caso, as configurações de um aplicativo devem ser mantidas externas a seu executável e lidas a partir de seu ambiente de runtime ou de uma fonte externa.

Embora qualquer aplicativo possa fazer uso da Configuração de Aplicativo, os exemplos a seguir são os tipos de aplicativo que se beneficiam desse uso:

* Microsserviços baseados no Serviço de Kubernetes do Azure, Azure Service Fabric ou em outros aplicativos em contêiner implantados em uma ou mais geografias
* Aplicativos sem servidor, que incluem o Azure Functions ou outros aplicativos de computação sem estado controlado por evento
* Pipeline de implantação contínua

A Configuração de Aplicativo oferece os seguintes benefícios:

* Um serviço totalmente gerenciado que pode ser configurado em minutos
* Representações e mapeamentos de chave flexíveis
* Marcação com rótulos
* Reprodução pontual de configurações
* Interface do usuário dedicada para o gerenciamento de sinalizadores de recurso
* Comparação de dois conjuntos de configurações em dimensões com definições personalizadas
* Segurança aprimorada por meio de identidades gerenciadas do Azure
* Criptografias de dados completas, em repouso ou em trânsito
* Integração nativa com estruturas populares

A Configuração de Aplicativo complementa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), que é usado para armazenar segredos do aplicativo. A Configuração de Aplicativo facilita a implementação dos seguintes cenários:

* Centralizar o gerenciamento e a distribuição de dados de configuração hierárquicos para diferentes ambientes e geografias
* Altere dinamicamente as configurações de aplicativo sem a necessidade de reimplantar nem reiniciar um aplicativo
* Controlar a disponibilidade de recursos em tempo real

## <a name="use-app-configuration"></a>Usar Configuração de Aplicativo

A maneira mais fácil de adicionar um repositório de Configuração de Aplicativos é por meio de uma biblioteca de clientes fornecida pela Microsoft. Com base na linguagem e estrutura de programação, os melhores métodos a seguir estão disponíveis para você.

| Estrutura e linguagem de programação | Como conectar-se |
|---|---|
| .NET Core e ASP.NET Core | Provedor de Configuração de Aplicativo para .NET Core |
| .NET Framework e ASP.NET | Construtor de Configuração de Aplicativo para .NET |
| Java Spring | Cliente de Configuração de Aplicativo para Spring Cloud |
| Outras pessoas | API REST de Configuração de Aplicativo |

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido do ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Início Rápido do .NET Core](./quickstart-dotnet-core-app.md)
* [Início Rápido do .NET Framework](./quickstart-dotnet-app.md)
* [Início rápido do Azure Functions](./quickstart-azure-functions-csharp.md)
* [Início Rápido do Java Spring](./quickstart-java-spring-app.md)
* [Início Rápido do sinalizador de recurso do ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Início rápido do sinalizador de recursos do Spring Boot](./quickstart-feature-flag-spring-boot.md)
