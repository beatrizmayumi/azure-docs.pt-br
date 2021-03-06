---
title: Visão geral das Tarefas do ACR
description: Uma introdução às tarefas do ACR, um conjunto de recursos no registro de contêiner do Azure que fornece criação de imagem de contêiner segura e automatizada, gerenciamento e aplicação de patches na nuvem.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: f8ab3c3bd259f83a61d0b030a49e158ccd6e2a69
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938873"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizar compilações de imagem de contêiner e manutenção com tarefas ACR

Os contêineres fornecem novos níveis de virtualização, isolando as dependências de aplicativo e desenvolvedor dos requisitos de infraestrutura e operacionais. No entanto, o que resta é a necessidade de abordar como essa virtualização de aplicativos é gerenciada e corrigida no ciclo de vida do contêiner.

## <a name="what-is-acr-tasks"></a>O que são as Tarefas do ACR?

As **Tarefas do ACR** são um pacote de recursos do Registro de Contêiner do Azure. Ele fornece criação de imagem de contêiner baseada em nuvem para [plataformas](#image-platforms) , incluindo Linux, Windows e ARM, e pode automatizar a [aplicação de patches do sistema operacional e da estrutura](#automate-os-and-framework-patching) para seus contêineres do Docker. As tarefas de ACR não apenas estende o ciclo de desenvolvimento de "loop interno" para a nuvem com compilações de imagem de contêiner sob demanda, mas também permite compilações automatizadas disparadas por atualizações de código-fonte, atualizações para a imagem base de um contêiner ou temporizadores. Por exemplo, com gatilhos de atualização de imagem base, você pode automatizar o fluxo de trabalho do sistema operacional e aplicação de patch de estrutura de aplicativo, mantendo ambientes seguros enquanto obedece aos princípios de contêineres imutáveis.

## <a name="task-scenarios"></a>Cenários de tarefas

As tarefas ACR dão suporte a vários cenários para criar e manter imagens de contêiner e outros artefatos. Consulte as seções a seguir neste artigo para obter detalhes.

* **[Tarefa rápida](#quick-task)** – crie e envie por push uma única imagem de contêiner para um registro de contêiner sob demanda, no Azure, sem a necessidade de uma instalação de mecanismo do Docker local. Pense em `docker build`, `docker push` na nuvem.
* **Tarefas disparadas automaticamente** – habilite um ou mais *gatilhos* para criar uma imagem:
  * **[Disparar na atualização do código-fonte](#trigger-task-on-source-code-update)** 
  * **[Disparar na atualização da imagem base](#automate-os-and-framework-patching)** 
  * **[Disparar em uma agenda](#schedule-a-task)** 
* **[Tarefa de várias etapas](#multi-step-tasks)** – estenda a funcionalidade de compilação e envio de imagem única de tarefas ACR com fluxos de trabalho de várias etapas com base em vários contêineres. 

Cada tarefa ACR tem um [contexto de código-fonte](#context-locations) associado-o local de um conjunto de arquivos de origem usados para criar uma imagem de contêiner ou outro artefato. Os contextos de exemplo incluem um repositório git ou um sistema de arquivos local.

As tarefas também podem aproveitar as [variáveis de execução](container-registry-tasks-reference-yaml.md#run-variables), para que você possa reutilizar definições de tarefas e padronizar marcas para imagens e artefatos.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento de loop interno, o processo iterativo de escrever código, compilar e testar o aplicativo antes de confirmar no controle do código-fonte, é realmente o início do gerenciamento de ciclo de vida do contêiner.

Antes que você confirme sua primeira linha de código, o recurso [tarefas rápidas](container-registry-tutorial-quick-task.md) das Tarefas do ACR pode fornecer uma experiência de desenvolvimento integrada ao descarregar os builds de imagem de contêiner no Azure. Com as tarefas rápidas, você pode verificar suas definições de build automatizadas e detectar possíveis problemas antes de confirmar o código.

Usando o conhecido formato de `docker build`, o comando [AZ ACR Build][az-acr-build] no CLI do Azure usa um [contexto](#context-locations) (o conjunto de arquivos a serem compilados), envia as tarefas de ACR e, por padrão, empurra a imagem interna para o registro após a conclusão.

Para obter uma introdução, consulte o guia de início rápido para [criar e executar uma imagem de contêiner](container-registry-quickstart-task-cli.md) no registro de contêiner do Azure.  

O recurso Tarefas de ACR foi projetado como uma primitiva do ciclo de vida do contêiner. Por exemplo, é possível integrar as Tarefas do ACR à sua solução de CI/CD. Ao executar o [AZ login][az-login] com uma [entidade de serviço][az-login-service-principal], sua solução de CI/CD poderá emitir comandos [AZ ACR Build][az-acr-build] para iniciar a criação de imagens.

Saiba como usar as tarefas rápidas no primeiro tutorial das Tarefas do ACR, [Compilar imagens de contêiner na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se você quiser criar e enviar por push uma imagem diretamente do código-fonte, sem um Dockerfile, o registro de contêiner do Azure fornecerá o comando [AZ ACR Pack Build][az-acr-pack-build] (versão prévia). Essa ferramenta cria e envia uma imagem do código-fonte do aplicativo usando a [nuvem nativa Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Disparar tarefa na atualização do código-fonte

Dispare uma compilação de imagem de contêiner ou uma tarefa de várias etapas quando o código é confirmado ou uma solicitação de pull é feita ou atualizada para um repositório git público ou privado no GitHub ou no Azure DevOps. Por exemplo, configure uma tarefa de compilação com o comando de CLI do Azure [AZ ACR Task Create][az-acr-task-create] especificando um repositório git e, opcionalmente, uma ramificação e Dockerfile. Quando a sua equipe atualiza o código no repositório, um webhook criado por tarefas de ACR dispara uma compilação da imagem de contêiner definida no repositório. 

As tarefas ACR dão suporte aos seguintes gatilhos quando você define um repositório Git como o contexto da tarefa:

| Gatilho | Habilitado por padrão |
| ------- | ------------------ |
| Commit | Sim |
| Solicitação pull | Não |

Para configurar um gatilho de atualização de código-fonte, você precisa fornecer à tarefa um PAT (token de acesso pessoal) para definir o webhook no GitHub público ou privado ou no repositório DevOps do Azure.

> [!NOTE]
> Atualmente, as Tarefas do ACR não dão suporte a gatilhos de solicitação de pull ou commit em Repos do GitHub Enterprise.

Saiba como disparar builds na confirmação do código-fonte no segundo tutorial das Tarefas do ACR, [Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar sistema operacional e aplicação de patch de estrutura

O poder das Tarefas do ACR de realmente aprimorar seu fluxo de trabalho de build de contêiner vem da capacidade de detectar uma atualização para uma imagem de base. Quando a imagem base atualizada é enviada para o registro ou uma imagem base é atualizada em um repositório público, como no Hub do Docker, as tarefas do ACR podem criar automaticamente qualquer imagem de aplicativo baseada nela.

As imagens de contêiner podem ser amplamente categorizadas em imagens de *base* e imagens de *aplicativo*. Suas imagens de base normalmente incluem o sistema operacional e estruturas de aplicativo com base nos quais seu aplicativo é criado, em conjunto com outras personalizações. Essas imagens básicas normalmente são baseadas em imagens de upstream públicas, por exemplo: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]ou [node. js][base-node]. Várias das suas imagens de aplicativo podem compartilhar uma imagem de base comum.

Quando um sistema operacional ou uma imagem de estrutura de aplicativo é atualizado pelo mantenedor upstream, por exemplo, com um patch de segurança crítico de sistema operacional, você deverá também atualizar suas imagens de base para incluir a correção crítica. Cada imagem de aplicativo deve também ser reconstruída para incluir essas correções upstream agora incluídas em sua imagem de base.

Como as Tarefas do ACR descobrem dinamicamente as dependências da imagem base quando criam uma imagem de contêiner, elas podem detectar quando a imagem base de uma imagem de aplicativo é atualizada. Com uma [tarefa de build](container-registry-tutorial-base-image-update.md#create-a-task) pré-configurada, as Tarefas do ACR **recriam automaticamente cada imagem do aplicativo** para você. Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

Para compilações de imagem de um Dockerfile, uma tarefa ACR rastreia uma atualização de imagem base quando a imagem base está em um dos seguintes locais:

* O mesmo Registro de Contêiner do Azure em que a tarefa é executada
* Outro Registro de Contêiner do Azure na mesma região 
* Um repositório público no Docker Hub
* Um repositório público no Registro de Contêiner da Microsoft

> [!NOTE]
> * O gatilho de atualização da imagem base é habilitado por padrão em uma tarefa ACR. 
> * Atualmente, as tarefas de ACR rastreiam apenas atualizações de imagem base para imagens de aplicativo (*tempo de execução*). As tarefas de ACR não rastreiam atualizações de imagem base para imagens intermediárias (*buildtime*) usadas em Dockerfiles de vários estágios. 

Saiba mais sobre aplicação de patches do sistema operacional e da estrutura no terceiro tutorial de tarefas do ACR, [Automatize a criação de imagens na atualização da imagem base com as tarefas do registro de contêiner do Azure](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Agendar uma tarefa

Opcionalmente, agende uma tarefa Configurando um ou mais *gatilhos de temporizador* ao criar ou atualizar a tarefa. Agendar uma tarefa é útil para executar cargas de trabalho de contêiner em um agendamento definido ou executar operações de manutenção ou testes em imagens enviadas por push regularmente para o registro. Para obter detalhes, consulte [executar uma tarefa ACR em um agendamento definido](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tarefas de várias etapas

As tarefas de várias etapas fornecem uma definição de tarefa baseada em etapas e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. As etapas de tarefa definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md) especificam operações individuais de criação e envio por push para imagens de contêiner ou outros artefatos. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

Por exemplo, você pode criar uma tarefa de várias etapa que automatiza o seguinte:

1. Compilar uma imagem do aplicativo Web
1. Executar o contêiner de aplicativo Web
1. Compilar uma imagem de teste do aplicativo Web
1. Executar o contêiner de teste de aplicativo Web, que executa testes em relação ao contêiner de aplicativo em execução
1. Quando os testes são aprovados, compilar um pacote de arquivo morto de gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo morto do gráfico do Helm

As tarefas de várias etapas permitem que você divida o build, a execução e o teste de uma imagem em etapas mais combináveis, com suporte para dependência entre etapas. Com as tarefas de várias etapas nas Tarefas do ACR, há um controle mais granular sobre os fluxos de trabalho de build de imagem, de teste e de aplicação de patch no sistema operacional e na estrutura.

Saiba mais sobre as tarefas de várias etapas em [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Executar tarefas de várias etapas de build, teste e patch nas Tarefas do ACR).

## <a name="context-locations"></a>Locais de contexto

A tabela a seguir mostra alguns exemplos de locais de contexto com suporte para as Tarefas do ACR:

| Local do contexto | Description | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch mestre do GitHub | Arquivos dentro da ramificação mestre (ou outro padrão) de um repositório GitHub público ou privado.  | `https://github.com/gituser/myapp-repo.git` |
| Ramificação GitHub | Branch específico de um repositório GitHub público ou privado.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repositório GitHub público ou privado. O exemplo mostra a combinação de uma especificação de ramificação e subpasta. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Confirmação do GitHub | Confirmação específica em um repositório GitHub público ou privado. O exemplo mostra a combinação de uma especificação de hash de confirmação (SHA) e de subpasta. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Subpasta DevOps do Azure | Arquivos dentro de uma subpasta em um repositório público ou privado do Azure. O exemplo mostra a combinação de especificação de ramificação e subpasta. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor remoto. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Ao usar um repositório git privado como um contexto para uma tarefa, você precisa fornecer um PAT (token de acesso pessoal).

## <a name="image-platforms"></a>Plataformas de imagem

Por padrão, as tarefas ACR criam imagens para o SO Linux e a arquitetura AMD64. Especifique a marca de `--platform` para criar imagens do Windows ou imagens do Linux para outras arquiteturas. Especifique o sistema operacional e, opcionalmente, uma arquitetura com suporte no formato de sistema operacional/arquitetura (por exemplo, `--platform Linux/arm`). Para arquiteturas ARM, especifique opcionalmente uma variante no formato do sistema operacional/arquitetura/variante (por exemplo, `--platform Linux/arm64/v8`):

| SISTEMA OPERACIONAL | Arquitetura|
| --- | ------- | 
| Linux | AMD64<br/>braço<br/>arm64<br/>386 |
| Windows | AMD64 |

## <a name="view-task-logs"></a>Exibir logs de tarefa

Cada execução de tarefa gera a saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com êxito. Se você usar o comando [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)ou [AZ ACR Task execute](/cli/azure/acr/task#az-acr-task-run) para disparar a tarefa, a saída do log para a execução da tarefa será transmitida ao console e também será armazenada para recuperação posterior. Quando uma tarefa é disparada automaticamente, por exemplo, por uma confirmação de código-fonte ou uma atualização de imagem base, os logs de tarefa são armazenados somente. Exiba os logs de uma tarefa executada no portal do Azure ou use o comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Por padrão, os dados e logs para tarefas executadas em um registro são mantidos por 30 dias e, em seguida, limpos automaticamente. Se você quiser arquivar os dados para uma execução de tarefa, habilite o arquivamento usando o comando [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . O exemplo a seguir habilita o arquivamento para a tarefa executar *CF11* no registro *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Próximos passos

Quando estiver pronto para automatizar as compilações e a manutenção da imagem de contêiner na nuvem, confira a [série de tutoriais de tarefas do ACR](container-registry-tutorial-quick-task.md).

Opcionalmente, instale a [Extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetue pull e push de imagens para um registro de contêiner do Azure ou execute Tarefas do ACR, tudo isso no Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
