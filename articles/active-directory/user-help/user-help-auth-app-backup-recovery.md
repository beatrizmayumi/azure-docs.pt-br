---
title: Fazer backup e recuperar contas com o aplicativo Microsoft Authenticator-Azure AD
description: Saiba como fazer backup e recuperar as credenciais de conta de backup, usando o aplicativo Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 8d4c3d76a1ff7c3fcedbb4d2c22a699f4a2e77d0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704775"
---
# <a name="backup-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Fazer backup e recuperar credenciais da conta usando o aplicativo Microsoft Authenticator

**Aplica-se a:**

- dispositivos iOS, executando a versão 5.7.0 e posterior

- Dispositivos Android, executando a versão 6.6.0 e posteriores

O aplicativo Microsoft Authenticator faz backup das credenciais da sua conta e das configurações de aplicativos relacionadas, como a ordem de suas contas, na nuvem. Após o backup, também será possível usar o aplicativo para recuperar as informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

Cada local de armazenamento de backup exige que você tenha um conta Microsoft pessoal, enquanto o iOS também exige que você tenha uma conta do iCloud. Você pode ter várias contas armazenadas nesse único local. Por exemplo, você pode ter uma conta pessoal, uma conta corporativa ou de estudante e uma pessoa, não conta Microsoft como o Facebook, o Google e assim por diante.

> [!IMPORTANT]
> Somente suas credenciais de conta pessoal e de terceiros são armazenadas, o que inclui seu nome de usuário e o código de verificação de conta que é necessário para provar sua identidade. Não armazenamos outras informações associadas às contas, incluindo emails ou arquivos. Também não associamos ou compartilhamos as contas de forma alguma ou com qualquer outro produto ou serviço. E, finalmente, o administrador de TI não receberá informações sobre nenhuma dessas contas.

## <a name="back-up-your-account-credentials"></a>Fazer backup das credenciais da conta

Antes de poder fazer backup das suas credenciais, você deve ter:

- Uma [conta pessoal da Microsoft](https://account.microsoft.com/account) para agir como uma conta de recuperação.

- **Somente para IOS,** você deve ter uma [conta do icloud](https://www.icloud.com/) para o local de armazenamento real.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Para ativar o backup na nuvem para dispositivos iOS

- Em seu dispositivo iOS, selecione **Configurações**, **Backup** e, em seguida, ative **Backup do iCloud**.

    O backup das credenciais da sua conta é feito para a conta do iCloud.

    ![Tela de configurações do iOS mostrando a localização das configurações de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Para ativar o backup na nuvem para dispositivos Android

- Em seu dispositivo Android, selecione **configurações**, selecione **backup**e, em seguida, ative o **backup na nuvem**.

    Suas credenciais de conta são submetidas a backup em sua conta de nuvem.

    ![Tela de configurações do Android, mostrando o local das configurações de backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo

Você pode recuperar suas credenciais de conta da sua conta de nuvem, mas deve primeiro verificar se a conta que você está recuperando não existe no aplicativo Microsoft Authenticator. Por exemplo, se você estiver recuperando sua conta Microsoft pessoal, certifique-se de que você não tem uma conta Microsoft pessoal já configurada no aplicativo autenticador. Essa verificação é importante, portanto, podemos ter certeza de que não estamos substituindo ou apagando uma conta existente por engano.

### <a name="to-recover-your-information"></a>Para recuperar as informações

1. Em seu dispositivo móvel, abra o aplicativo Microsoft Authenticator e selecione **Iniciar recuperação** na parte inferior da tela.

    ![Aplicativo Microsoft Authenticator, mostrando onde clicar em Iniciar recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Entre na conta de recuperação, usando a mesma conta pessoal da Microsoft utilizada durante o processo de backup.

    As credenciais da conta são recuperadas para o novo dispositivo.

Após concluir a recuperação, você poderá notar que os códigos de verificação de conta pessoal da Microsoft no aplicativo Microsoft Authenticator são diferentes entre os telefones novos e antigos. Os códigos são diferentes porque cada dispositivo tem sua própria credencial exclusiva, mas ambos são válidos e funcionam durante a autenticação usando o telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais que exigem mais verificação

Se você usar notificações por push com suas contas pessoais ou corporativas, obterá um alerta na tela que diz que você deve fornecer verificação adicional antes de recuperar suas informações. Como as notificações por push exigem o uso de uma credencial vinculada ao dispositivo específico e nunca enviada pela rede, você deverá provar sua identidade antes que a credencial seja criada no dispositivo.

Para contas pessoais da Microsoft, você pode provar sua identidade inserindo a senha junto com um email ou número de telefone alternativo. Para contas corporativas ou de estudante, você deverá digitalizar um código QR fornecido pelo provedor de conta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Para fornecer verificação adicional para contas pessoais

1. Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa próxima à conta que você deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Selecione **Entrar para recuperar**, digite a senha e confirme o endereço de email ou o número de telefone como verificação adicional.

    ![Aplicativo Microsoft Authenticator, permitindo que você insira as informações de entrada](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Para fornecer verificação adicional para contas corporativas ou de estudante

1. Na tela **Contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa próxima à conta que você deseja recuperar.

    ![Aplicativo Microsoft Authenticator, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Selecione **Digitalizar código QR para recuperar** e, em seguida, digitalize o código QR.

    ![Aplicativo Microsoft Authenticator, permitindo que você digitalize o código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Para obter mais informações sobre códigos QR e como obter um, consulte Introdução [ao aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) ou [configurar informações de segurança para usar um aplicativo autenticador](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), com base em se o administrador ativou as informações de segurança.
    >
    >Se esta for a primeira vez que está configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou que o aplicativo tire fotos e grave vídeo (Android). Você precisa selecionar **Permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma foto do código QR na próxima etapa. Se não permitir acesso da câmera, você ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, confira [Adicionar uma conta manualmente ao aplicativo](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Solucionar problemas de backup e recuperação

Há alguns motivos pelos quais o backup pode não estar disponível:

- **Alterar sistemas operacionais.** O backup é armazenado no iCloud para iOS e no provedor de armazenamento em nuvem da Microsoft para Android. Isso significa que o backup não estará disponível se você alternar entre dispositivos Android e iOS. Se você fizer a opção, deverá recriar manualmente suas contas no aplicativo Microsoft Authenticator.

- **Problemas de rede.** Se você estiver enfrentando problemas relacionados à rede, verifique se você está conectado à rede e conectado corretamente à sua conta.

- **Problemas de conta.** Se você estiver enfrentando problemas relacionados à conta, verifique se você está conectado corretamente à sua conta. Para o iOS, isso significa que você deve estar conectado ao iCloud usando a mesma conta Appleid que o iPhone.

- **Exclusão acidental.** É possível que você tenha excluído a conta de backup do dispositivo anterior ou ao gerenciar a conta de armazenamento em nuvem. Nessa situação, será necessário recriar manualmente a conta no aplicativo.

- **Contas existentes do Microsoft Authenticator.** Se você já configurou contas no aplicativo Microsoft Authenticator, o aplicativo não poderá recuperar as contas de backup. Impedir a recuperação ajuda a garantir que os detalhes da conta não sejam substituídos por informações desatualizadas. Nessa situação, antes de recuperar o backup você deverá remover todas as informações de conta existentes das contas existentes configuradas no aplicativo Authenticator.

- **O backup está desatualizado.** Se suas informações de backup estiverem desatualizadas, você poderá ser solicitado a atualizar as informações entrando novamente na sua conta de recuperação da Microsoft. Sua conta de recuperação é a conta Microsoft pessoal usada inicialmente para armazenar o backup. Se uma entrada for necessária, você verá um ponto vermelho no menu ou na barra de ação. Depois de selecionar o ponto vermelho, você será solicitado a entrar novamente para atualizar suas informações.

## <a name="next-steps"></a>Próximos passos

Agora que você fez backup e recuperou as credenciais da conta para o novo dispositivo, continue usando o aplicativo Microsoft Authenticator para verificar a identidade. Para obter mais informações, consulte [entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Artigos relacionados

- [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)

- [Autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/)
