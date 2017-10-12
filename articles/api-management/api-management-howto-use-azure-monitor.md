---
title: "Наблюдение за управлением API с помощью Azure Monitor | Документация Майкрософт"
description: "Узнайте, как наблюдать за службой управления API Azure с помощью Azure Monitor."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Наблюдение за управлением API с помощью Azure Monitor
Azure Monitor — это служба Azure, которая предоставляет единый источник данных для наблюдения за всеми ресурсами Azure. С помощью Azure Monitor можно визуализировать, запрашивать, маршрутизировать, архивировать метрики и журналы, полученные от ресурсов Azure, таких как управление API, а также реагировать на них. 

В следующем видео показано, как отслеживать управление API с помощью Azure Monitor. Дополнительные сведения об Azure Monitor см. в статье [Приступая к работе с Azure Monitor]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Метрики
Сейчас управление API передает пять метрик. В будущем мы планируем добавить больше метрик. Эти метрики передаются ежеминутно, что позволяет в близком к реальному времени отслеживать состояние и работоспособность интерфейсов API. Ниже приведены сводные сведения о метриках.
* Общее количество запросов к шлюзу. Количество запросов API за период. 
* Успешные запросы к шлюзу. Количество запросов API, которые успешно получили коды ответов HTTP, включая 304, 307 и все, что меньше, чем 301 (например, 200). 
* Неудачные запросы к шлюзу. Количество запросов API, которые получили ошибочные коды ответов HTTP, включая 400 и все, что больше, чем 500.
* Неавторизованные запросы к шлюзу. Количество запросов API, которые получили коды ответов HTTP, включая 401, 403 и 429. 
* Другие запросы к шлюзу. Количество запросов API, которые получили коды ответов HTTP, не принадлежащие ни к одной из указанных выше категорий (например, 418).

Вы можете получить доступ к метрикам в службе управления API или получить доступ к метрикам всех ресурсов Azure в Azure Monitor. Чтобы просмотреть метрики в службе управления API, сделайте следующее:
1. Перейдите на портал Azure.
2. Перейдите к службе управления API.
3. Щелкните **Метрики**.

![Колонка метрик][metrics-blade]

Дополнительные сведения об использовании метрик см. в статье [Обзор метрик в Microsoft Azure].

## <a name="activity-logs"></a>Журналы действий
Журналы действий позволяют подробно проанализировать операции, выполненные в службах управления API. Ранее они назывались журналами аудита или журналами операций. С помощью журналов изменений можно ответить на вопросы "что? кто? когда?" о любой операции записи (PUT, POST, DELETE) в службах управления API. 

> [!NOTE]
> Журналы действий не содержат операций чтения (GET), операций, выполненных на классическом портале издателя или с помощью исходных API управления.

Вы можете получить доступ к журналам действий в службе управления API или получить доступ к журналам всех ресурсов Azure в Azure Monitor. Чтобы просмотреть журналы действий в службе управления API, сделайте следующее:
1. Перейдите на портал Azure.
2. Перейдите к службе управления API.
3. Щелкните **Журнал действий**.

![Колонка журналов действий][activity-logs-blade]

Дополнительные сведения об использовании метрик см. в статье [Общие сведения о журнале действий Azure].

## <a name="alerts"></a>Оповещения
Вы можете настроить получение уведомлений на основе метрик и журналов действий. Azure Monitor позволяет настроить действие, выполняемое при активации оповещения:

* Отправка уведомления по электронной почте.
* Вызов webhook.
* Вызов приложения логики Azure.

Правила оповещений можно настроить в службе управления API или в Azure Monitor. Чтобы настроить их в службе управления API: 
1. Перейдите на портал Azure.
2. Перейдите к службе управления API.
3. Щелкните **Правила оповещения**.

![Колонка "Правила оповещения"][alert-rules-blade]

Дополнительные сведения об использовании оповещений см. в статье [Создание оповещений в Azure Monitor для служб Azure с помощью портала Azure].

## <a name="diagnostic-logs"></a>Журналы диагностики
Журналы диагностики предоставляют подробные сведения об операциях и ошибках, которые важны для аудита, а также для устранения неполадок. Журналы диагностики отличаются от журналов действий. Журналы действий позволяют подробно проанализировать операции, выполненные с ресурсами Azure. Журналы диагностики дают представление об операциях, выполняемых самим ресурсом.

Сейчас управление API предоставляет журналы диагностики (выполняемые в пакетном режиме каждый час) отдельных запросов API со следующей структурой каждой записи:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

Вы можете получить доступ к журналам диагностики в службе управления API или получить доступ к журналам всех ресурсов Azure в Azure Monitor. Чтобы просмотреть журналы диагностики в службе управления API, сделайте следующее:
1. Перейдите на портал Azure.
2. Перейдите к службе управления API.
3. Щелкните **Журналы диагностики**.

![Колонка "Журналы диагностики"][diagnostic-logs-blade]

Дополнительные сведения об использовании метрик см. в статье [Сбор и использование диагностических данных из ресурсов Azure].

## <a name="next-step"></a>Дальнейшее действие

* [Приступая к работе с Azure Monitor]
* [Обзор метрик в Microsoft Azure]
* [Общие сведения о журнале действий Azure]
* [Сбор и использование диагностических данных из ресурсов Azure]
* [Создание оповещений в Azure Monitor для служб Azure с помощью портала Azure]

[Приступая к работе с Azure Monitor]: ../monitoring-and-diagnostics/monitoring-get-started.md
[Обзор метрик в Microsoft Azure]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[Общие сведения о журнале действий Azure]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[Сбор и использование диагностических данных из ресурсов Azure]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[Создание оповещений в Azure Monitor для служб Azure с помощью портала Azure]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
