---
title: "Журналы диагностики служебной шины Azure | Документация Майкрософт"
description: "Узнайте, как анализировать журналы диагностики из служебной шины в Microsoft Azure."
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Журналы диагностики служебной шины

## <a name="introduction"></a>Введение
Служебная шина предоставляет журналы двух типов: 
* [журналы действий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), которые всегда включены и содержат информацию об операциях, выполняемых заданиями;
* [журналы диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), которые настраиваются пользователем и содержат более полную информацию обо всем, что происходит с заданием после его создания, при обновлении, выполнении и вплоть до удаления.

## <a name="how-to-enable-diagnostic-logs"></a>Как включить журналы диагностики
По умолчанию журналы диагностики **отключены**. Чтобы включить их, выполните следующее.

Войдите на портал Azure и перейдите к колонке задания потоковой передачи. Выберите колонку "Журналы диагностики" в разделе "Мониторинг".

![Перемещение к колонке журналов диагностики](./media/service-bus-diagnostic-logs/image1.png)  

Затем щелкните ссылку "Включить диагностику".

![Включение журналов диагностики](./media/service-bus-diagnostic-logs/image2.png)

В появившемся окне "Диагностика" измените состояние на "Включено".

![Изменение состояния журналов диагностики](./media/service-bus-diagnostic-logs/image3.png)

Настройте требуемую цель архивации (учетная запись хранения, концентратор событий, Log Analytics) и выберите категории журналов, которые нужно собирать ("Выполнение", "Разработка"). Сохраните новую конфигурацию системы диагностики.

После сохранения конфигурация вступит в силу примерно через 10 минут. После этого журналы начнут отображаться в настроенной цели архивации, которую можно просмотреть в колонке "Журналы диагностики".

Дополнительные сведения о настройке системы диагностики доступны на странице про [журналы диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-schema"></a>Схема журналов диагностики

Все журналы хранятся в формате JSON, и каждая запись содержит строковые поля, имеющие приведенный ниже формат.

### <a name="operation-logs"></a>Журналы операций

OperationalLogs записывает в журнал все, что связано с операциями служебной шины, в частности тип операций, например создание очереди, используемые ресурсы и состояние операций.

Имя | Описание
------- | -------
ActivityId | Внутренний идентификатор для отслеживания
EventName | Имя операции             
resourceId | Идентификатор ресурса ARM
SubscriptionId | идентификатор подписки;
EventTimeString | Время операции
EventProperties | Свойства операции
Состояние | Состояние операции
Caller | Объект, вызывающий операцию (портал или клиент управления)
category | OperationalLogs

#### <a name="example-operation-log"></a>Пример журнала операций

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Дальнейшие действия
* [Основные сведения о служебной шине](service-bus-messaging-overview.md)
* [Приступая к работе со служебной шиной](service-bus-create-namespace-portal.md)

