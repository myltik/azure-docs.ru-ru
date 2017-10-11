---
title: "Потоковая передача журнала действий Azure в концентраторы событий | Документация Майкрософт"
description: "Узнайте, как настроить потоковую передачу журнала действий Azure в концентраторы событий."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/06/2017
ms.author: johnkem
ms.openlocfilehash: 88c5701279f370914fac68872d67b02a7571748a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Потоковая передача журнала действий Azure в концентраторы событий
[**Журнал действий Azure**](monitoring-overview-activity-logs.md) можно передавать в близком к реальному времени в любое приложение. Для этого следует настроить стандартный параметр "Экспорт" на портале или включить идентификатор правила служебной шины в профиле журнала с помощью командлетов Azure PowerShell или интерфейса командной строки Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Что можно делать с журналом действий и концентраторами событий
Мы приведем лишь несколько примеров использования потоковой передачи журнала действий.

* **Потоковая передача в сторонние системы ведения журналов и сбора телеметрии.** Через некоторое время концентраторы событий будут использоваться для передачи журнала действий в сторонние решения SIEM и решения для анализа журналов.
* **Создание пользовательской платформы для телеметрии и ведения журнала.** Если у вас уже есть пользовательская платформа для телеметрии и ведения журнала или вы только планируете ее создать, высокая масштабируемость публикации и подписки концентраторов событий позволит вам гибко настраивать прием журнала действий. [Ознакомьтесь с руководством Дэна Росановы (Dan Rosanova) по использованию концентраторов событий для глобальной платформы телеметрии.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Включение потоковой передачи журнала действий
Потоковую передачу журнала действий можно включить программно или с помощью портала. В любом случае необходимо выбрать пространство имен служебной шины и политику общего доступа для него, после чего при возникновении нового события журнала действий в этом пространстве имен создается концентратор событий. Если у вас нет пространства имен служебной шины, необходимо сначала создать его. Если потоковая передача событий журнала действий уже выполнялась в это пространство имен служебной шины, созданный ранее концентратор событий будет использован повторно. Политика общего доступа определяет разрешения, предоставленные для механизма потоковой передачи. В настоящее время для потоковой передачи журналов в концентраторы событий нужны разрешения **Управление**, **Отправка** и **Прослушивание**. Политики общего доступа для пространства имен служебной шины можно создать или изменить на классическом портале на вкладке "Настройка" для пространства имен служебной шины. Чтобы изменить профиль журнала действий и разрешить потоковую передачу, пользователь, который вносит изменение, должен иметь разрешение ListKey для правила авторизации служебной шины.

Служебная шина или пространство имен концентратора событий не обязательно должны находиться в той самой подписке, в которой создаются журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.

### <a name="via-azure-portal"></a>С помощью портала Azure
1. Перейдите к колонке **Журнал действий** с помощью меню в левой части портала.
   
    ![Переход к журналу действий на портале](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Нажмите кнопку **Экспорт** в верхней части колонки.
   
    ![Кнопка экспорта на портале](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Откроется колонка, в которой вы можете выбрать регионы, для которых нужно выполнять передачу событий, и пространство имен служебной шины, в котором вы хотите создать концентратор событий для потоковой передачи этих событий.
   
    ![Колонка экспорта журнала действий](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Нажмите кнопку **Сохранить** , чтобы сохранить эти параметры. Параметры будут немедленно применены к подписке

### <a name="via-powershell-cmdlets"></a>С помощью командлетов PowerShell
Если профиль журнала уже существует, следует сначала удалить этот профиль.

1. Используйте `Get-AzureRmLogProfile` , чтобы проверить, существует ли профиль журнала.
2. Если он есть, используйте `Remove-AzureRmLogProfile` , чтобы удалить его.
3. Используйте `Set-AzureRmLogProfile`, чтобы создать профиль:

```
Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

ServiceBusRuleID — это строка в таком формате: {идентификатор_ресурса_служебной_шины} /authorizationrules/{имя_ключа}. Например: 

### <a name="via-azure-cli"></a>С помощью интерфейса командной строки Azure
Если профиль журнала уже существует, следует сначала удалить этот профиль.

1. Используйте `azure insights logprofile list` , чтобы проверить, существует ли профиль журнала.
2. Если он есть, используйте `azure insights logprofile delete` , чтобы удалить его.
3. Используйте `azure insights logprofile add` , чтобы создать профиль:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

ServiceBusRuleID — это строка в таком формате: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Как используются данные журнала из концентраторов событий?
Схема для журнала действий доступна [здесь](monitoring-overview-activity-logs.md). Каждое событие сохраняется в массиве больших двоичных объектов JSON, которые называются "записями".

## <a name="next-steps"></a>Дальнейшие действия
* [Архивация журнала действий Azure](monitoring-archive-activity-log.md)
* [Общие сведения о журнале действий Azure](monitoring-overview-activity-logs.md)
* [Настройка объекта webhook для оповещений журнала действий Azure](insights-auditlog-to-webhook-email.md)

