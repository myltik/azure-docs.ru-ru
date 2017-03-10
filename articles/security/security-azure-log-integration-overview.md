---
title: "Интеграция журналов из ресурсов Azure в системы SIEM | Документация Майкрософт"
description: "Узнайте о службе интеграции журналов Azure, ее основных возможностях и принципах работы."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 4652795b8bef910b4e5eb83ef836e7b75621ab8a
ms.openlocfilehash: bbea6700f7abc7822c3530f151c224cb59104cb1
ms.lasthandoff: 02/23/2017


---
# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Введение в службу интеграции журналов Microsoft Azure (предварительная версия)
Узнайте о службе интеграции журналов Azure, ее основных возможностях и принципах работы.

## <a name="overview"></a>Обзор
Платформа как услуга (PaaS) и инфраструктура как услуга (IaaS), размещенные в Azure, создают большой объем данных в журналах безопасности. Эти журналы содержат важную информацию, позволяющую получить ценные сведения о нарушениях политик, внутренних и внешних угрозах, проблемах соответствия нормативным требованиям и аномалиях в работе сети, узлов и активности пользователей.

Служба интеграции журналов Azure позволяет интегрировать необработанные журналы из ресурсов Azure с локальными системами SIEM (Security Information and Event Management). Она собирает данные системы диагностики Azure из виртуальных машин Windows *(WAD)*, журналов действий Azure, оповещений центра безопасности Azure и журналов поставщика ресурсов Azure. С помощью такой интеграции вы можете получить доступ ко всем своим ресурсам, локальным или облачным, на единой панели мониторинга, что позволяет выполнять статистическую обработку, сопоставление и анализ, а также предупреждать о событиях безопасности.

![Служба интеграции журналов Azure][1]

## <a name="what-logs-can-i-integrate"></a>Какие журналы можно интегрировать?
Azure создает подробные журналы для каждой службы Azure. Они подразделяются на следующие две основные категории:

* **Журналы управления и контроля**, которые предоставляют информацию о таких операциях Azure Resource Manager, как CREATE, UPDATE и DELETE. К этому типу, например, относятся журналы аудита Azure.
* **Журналы плоскости данных**, которые предоставляют информацию о событиях, возникающих при использовании ресурса Azure. Например, к этому типу относятся журналы событий Windows для системы, безопасности и приложений на виртуальной машине.

В настоящее время служба интеграция журналов Azure поддерживает интеграцию журналов аудита Azure, журналов виртуальных машин и оповещений центра безопасности Azure.

Если у вас есть вопросы о службе интеграции журналов Azure, отправьте электронное сообщение на адрес [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы ознакомились со службой интеграции журналов Azure. Чтобы узнать больше о службе интеграции журналов Azure и поддерживаемых типах журналов, ознакомьтесь с приведенными ниже статьями.

* [Microsoft Azure Log Integration for Azure logs (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) (Служба интеграции журналов Microsoft Azure для журналов Azure (предварительная версия)) — посетите Центр загрузки, чтобы получить дополнительные сведения, изучить требования к системе и инструкции по установке службы интеграции журналов Azure.
* [Приступая к работе со службой интеграции журналов Azure](security-azure-log-integration-get-started.md) — в этом руководстве рассматривается установка службы интеграции журналов Azure и интеграция журналов из службы хранилища Azure WAD, журналов действий Azure, оповещений центра безопасности Azure и журналов аудита Azure Active Directory.
* [Azure Log Integration SIEM configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Настройка SIEM для службы интеграции журналов Azure) — в этой записи блога показано, как настроить службу интеграции журналов Azure для работы с такими решениями партнеров, как Splunk, HP ArcSight и IBM QRadar.
* [Azure log integration frequently asked questions (FAQ)](security-azure-log-integration-faq.md) (Служба интеграции журналов Azure: часто задаваемые вопросы) — эта статья содержит ответы на часто задаваемые вопросы об интеграции журналов Azure.
* [Интеграция оповещений центра обеспечения безопасности с помощью интеграции журналов Azure (предварительная версия)](../security-center/security-center-integrating-alerts-with-log-integration.md) — в этом документе показано, как синхронизировать оповещения центра безопасности, а также события безопасности виртуальных машин, собранные системой диагностики Azure и в журналах аудита Azure, с решением Log Analytics или SIEM.
* [New features for Azure diagnostics and Azure Audit logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Новые возможности системы диагностики Azure и журналов аудита Azure) — в этой записи блога рассказывается о журналах аудита Azure и других функциях, которые помогут глубже понять, как работают ваши ресурсы Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

