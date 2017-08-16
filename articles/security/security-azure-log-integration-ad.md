---
title: "Интеграция журналов Azure (AZLOG) с журналами аудита Active Directory | Документация Майкрософт"
description: "Узнайте, как установить службу интеграции журналов Azure и интегрировать журналы аудита Azure."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 9a4fa6ea1784ab0247659d5498c6fe3ae30355c4
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---

#<a name="integrate-azure-active-directory-audit-logs"></a>Интеграция журналов аудита Azure Active Directory

События аудита Azure Active Directory помогают определить привилегированные действия, выполняемые в Azure Active Directory. Просмотреть типы событий, доступных для отслеживания, можно с помощью [событий отчета об аудите Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

>[!NOTE]
Перед выполнением действий в этой статье просмотрите статью [Интеграция журналов Azure с ведением журнала системы диагностики Azure и пересылкой событий Windows](security-azure-log-integration-get-started.md) и выполните указанные в ней действия.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Интеграция журналов аудита Azure Active Directory

1. Откройте окно командной строки и с помощью команды cd перейдите в каталог **c:\Program Files\Microsoft Azure Log Integration**.
2. Выполните приведенную ниже команду.

 ``azlog createazureid``

 Эта команда запрашивает имя для входа Azure. Затем она создает субъект-службу Azure Active Directory в клиентах Azure AD, содержащих подписки Azure, для которых вошедший пользователь является администратором, соадминистратором или владельцем. Команда завершится ошибкой, если пользователь является только гостем в клиенте Azure AD. Аутентификация в Azure осуществляется через Azure Active Directory (AD). Создание субъекта-службы для службы интеграции Azlog приводит к созданию удостоверения Azure AD, которому предоставлен доступ на чтение из подписок Azure.

3. Выполните команду, указав идентификатор клиента. Для выполнения команды необходимо быть членом роли администратора клиента.

``Azlog.exe authorizedirectoryreader tenantId``

Пример

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

Проверьте следующие папки, в которых должны создаваться JSON-файлы журнала аудита Azure Active Directory:

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

В видео ниже демонстрируется выполнение действий, описанных в этой статье.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


>[!NOTE]
За инструкциями по переносу данных JSON-файлов в SIEM обратитесь к поставщику SIEM.

Помощь сообщества можно получить на [форуме MSDN по интеграции журналов Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). На этом форуме участники сообщества AzLog могут помогать друг другу, делясь вопросами, ответами, советами и рекомендациями о том, как максимально использовать возможности интеграции журналов Azure. Кроме того, команда разработчиков службы интеграции журналов Azure отслеживает этот форум и помогает при любой возможности.

Можно также отправить [запрос в службу поддержки](../azure-supportability/how-to-create-azure-support-request.md). Для этого укажите в запросе службу **Интеграции журналов**.

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше об интеграции журналов Azure, ознакомьтесь со следующими документами:

* [Служба интеграции журналов Microsoft Azure для журналов Azure](https://www.microsoft.com/download/details.aspx?id=53324) — посетите Центр загрузки, чтобы получить дополнительные сведения, изучить требования к системе и получить инструкции по установке службы интеграции журналов Azure.
* [Introduction to Microsoft Azure log integration (Preview)](security-azure-log-integration-overview.md) (Введение в службу интеграции журналов Azure (предварительная версия)) — в этом документе рассказывается о службе интеграции журналов Azure, ее основных возможностях и принципах работы.
* [Azure Log Integration SIEM configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Настройка SIEM для службы интеграции журналов Azure) — в этой записи блога показано, как настроить службу интеграции журналов Azure для работы с такими решениями партнеров, как Splunk, HP ArcSight и IBM QRadar.
* [Azure log integration frequently asked questions (FAQ)](security-azure-log-integration-faq.md) (Служба интеграции журналов Azure: часто задаваемые вопросы) — эта статья содержит ответы на часто задаваемые вопросы об интеграции журналов Azure.
* [Интеграция оповещений центра обеспечения безопасности с помощью интеграции журналов Azure (предварительная версия)](../security-center/security-center-integrating-alerts-with-log-integration.md) — в этом документе показано, как синхронизировать оповещения центра безопасности, а также события безопасности виртуальных машин, собранные системой диагностики Azure и в журналах аудита Azure, с решением Log Analytics или SIEM.
* [New features for Azure diagnostics and Azure Audit logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Новые возможности системы диагностики Azure и журналов аудита Azure) — в этой записи блога рассказывается о журналах аудита Azure и других функциях, которые помогут глубже понять, как работают ваши ресурсы Azure.

