---
title: Integrar o Splunk usando o Azure Monitor | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com o SumoLogic usando Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7da3a545847382f8fed192a6ec4fe2ac75bb8b35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014404"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Como integrar logs de Azure Active Directory com o Splunk usando Azure Monitor

Neste artigo, você aprenderá a integrar os logs do Azure Active Directory (Microsoft Azure AD) ao Splunk usando o Azure Monitor. Primeiro encaminhe os logs para um hub de eventos do Azure e, em seguida, integre o hub de eventos Splunk.

## <a name="prerequisites"></a>pré-requisitos

Para usar esse recurso, você precisa de:
* Um hub de eventos do azure contendo logs de atividades do Microsoft Azure AD. Saiba como [enviar seus logs de atividades para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Complemento Azure Monitor para Splunk. [Baixar e configurar sua instância do Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrar logs de Azure Active Directory 

1. Abra sua instância do Splunk e clique em **Resumo dos dados**.

    ![Botão "Resumo de dados"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecione a guia **Sourcetypes** e, em seguida, selecione **amal: aadal:audit**

    ![Na guia Sourcetypes de resumo de dados](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Você verá que os logs de atividade do Azure Active Directory são mostrados na figura a seguir:

    ![Logs de atividade](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se você não puder instalar um complemento em sua instância do Splunk (por exemplo, se estiver usando um proxy ou em execução no Splunk Cloud), poderá encaminhar esses eventos para o Coletor de Eventos HTTP. Para fazer isso, use esta [função do Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é disparada por novas mensagens no hub de eventos. 
>

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)