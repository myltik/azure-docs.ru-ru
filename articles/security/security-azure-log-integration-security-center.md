---
title: Объединение службы интеграции журналов Azure с центром безопасности | Документация Майкрософт
description: Узнайте, как объединить оповещения центра безопасности Azure со службой интеграции журналов.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29138482"
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Как добавить оповещения центра безопасности Azure в службу интеграции журналов Azure

В этой статье описывается, как сделать так, чтобы служба интеграции журналов Azure могла получать данные оповещений безопасности, создаваемых центром безопасности Azure. Необходимо успешно выполнить инструкции, описанные в [руководстве по началу работы](security-azure-log-integration-get-started.md), прежде чем приступить к действиям в этой статье.

## <a name="detailed-steps"></a>Подробные инструкции

Ниже описывается, как создать необходимый субъект-службу Azure Active Directory и назначить ему разрешения на чтение для подписки.
1. Откройте окно командной строки и перейдите в каталог **:\Program Files\Microsoft Azure Log Integration**.
2. Выполните команду ``azlog createazureid``.

    Эта команда запрашивает имя для входа Azure. Затем она создает [субъект-службу Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) в клиентах Azure AD, содержащих подписки Azure, для которых вошедший пользователь является администратором, соадминистратором или владельцем. Команда завершится ошибкой, если пользователь является только гостем в клиенте Azure AD. Аутентификация в Azure осуществляется через Azure Active Directory (AD). Создание субъекта-службы для службы интеграции Azlog приводит к созданию удостоверения Azure AD, которому будет предоставлен доступ на чтение из подписок Azure.

3. Затем следует выполнить команду, которая предоставляет только что созданному субъекту-службе доступ на чтение к подписке. Если не указать SubscriptionID, то эта команда попытается назначить субъекту-службе роль "Читатель" для всех подписок, к которым у вас есть какой-либо доступ. </br></br>
``azlog authorize <SubscriptionID>`` </br> Например: </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Если выполнить команду authorize сразу же после команды ```createazureid```, то могут появиться предупреждения. После создания учетная запись Azure AD становится доступной для использования с некоторой задержкой. Чтобы избежать таких предупреждений, следует подождать около 60 секунд после выполнения команды ```createazureid``` и лишь затем выполнять команду authorize.

4. Проверьте следующие папки, в которых должны находиться JSON-файлы журнала аудита:
 * **c:\Users\azlog\AzureResourceManagerJson;**
 * **c:\Users\azlog\AzureResourceManagerJsonLD.** </br></br>
5. Проверьте следующие папки, в которых должны находиться оповещения центра безопасности:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD.** </br></br>

В случае проблем при установке и настройке создайте [запрос в службу поддержки](/azure-supportability/how-to-create-azure-support-request.md), указав **Интеграция журнала** в качестве службы, для которой запрашивается поддержка.

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше об интеграции журналов Azure, ознакомьтесь со следующими документами:

* [Служба интеграции журналов Microsoft Azure для журналов Azure](https://www.microsoft.com/download/details.aspx?id=53324) — посетите Центр загрузки, чтобы получить дополнительные сведения, изучить требования к системе и получить инструкции по установке службы интеграции журналов Azure.
* [Introduction to Microsoft Azure log integration (Preview)](security-azure-log-integration-overview.md) (Введение в службу интеграции журналов Azure (предварительная версия)) — в этом документе рассказывается о службе интеграции журналов Azure, ее основных возможностях и принципах работы.
* [Azure Log Integration SIEM configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Настройка SIEM для службы интеграции журналов Azure) — в этой записи блога показано, как настроить службу интеграции журналов Azure для работы с такими решениями партнеров, как Splunk, HP ArcSight и IBM QRadar.
* [Azure log integration frequently asked questions (FAQ)](security-azure-log-integration-faq.md) (Служба интеграции журналов Azure: часто задаваемые вопросы) — эта статья содержит ответы на часто задаваемые вопросы об интеграции журналов Azure.
* [New features for Azure diagnostics and Azure Audit logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Новые возможности системы диагностики Azure и журналов аудита Azure) — в этой записи блога рассказывается о журналах аудита Azure и других функциях, которые помогут глубже понять, как работают ваши ресурсы Azure.
