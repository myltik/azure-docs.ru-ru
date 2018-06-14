---
title: Экспорт данных безопасности Azure в SIEM. Конфигурация конвейера [предварительная версия] | Документация Майкрософт
description: В этой статье описывается процедура передачи журналов центра безопасности Azure в SIEM.
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: 7a0a72a25010952f13eb190f0e0a1a65cc6d42d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124839"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Экспорт данных безопасности Azure в SIEM. Конфигурация конвейера [предварительная версия]

В этом документе описана процедура экспорта данных безопасности из центра безопасности Azure в SIEM.

Обработанные события, создаваемые центром безопасности Azure, публикуются в [журнале действий](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Azure, одном из различных журналов, доступных через Azure Monitor. Azure Monitor предлагает объединенный конвейер для передачи всех данных мониторинга в инструмент SIEM. Для этого используется потоковая передача данных мониторинга в концентратор событий, из которого они затем могут быть извлечены в инструмент партнера.

Этот канал использует [отдельный конвейер мониторинга Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) для получения доступа к данным мониторинга из среды Azure. Это позволяет легко настроить компоненты SIEM и инструменты мониторинга для работы с данными.

В следующих разделах описывается, как настроить потоковую передачу данных в концентратор событий. В инструкциях предполагается, что в вашей подписке Azure настроен центр безопасности Azure.

Общий обзор

![Общий обзор](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Какие данные безопасности Azure передаются в SIEM?

В этой предварительной версии предоставляются [оповещения системы безопасности](../security-center/security-center-managing-and-responding-alerts.md). В будущих выпусках мы дополним их рекомендациями по безопасности.

## <a name="how-to-setup-the-pipeline"></a>Как можно настроить конвейер? 

### <a name="create-an-event-hub"></a>Создание концентратора событий 

Вначале следует [создать пространство имен концентраторов событий](../event-hubs/event-hubs-create.md). Это пространство имен и концентратор событий — место назначения всех ваших данных мониторинга.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Потоковая передача журнала действий Azure в концентраторы событий

Ознакомьтесь со статьей [Потоковая передача журналов действий в концентраторы событий](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).

### <a name="install-a-partner-siem-connector"></a>Установка соединителя SIEM партнера 

Маршрутизация данных мониторинга в концентратор событий с помощью Azure Monitor обеспечивает интеграцию с партнерским решением SIEM и инструментами мониторинга.

Воспользуйтесь приведенной ссылкой, чтобы просмотреть список [поддерживаемых решений SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

## <a name="example-for-querying-data"></a>Пример запроса данных 

Ниже приведено несколько запросов Splunk, которые можно использовать для извлечения данных оповещений.

| **Описание запроса**                                | **Запрос**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Все оповещения.                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Суммирование количества операций по имени.             | index=main sourcetype="amal:security" \| table operationName \| stats count by operationName                                |
| Получение сведений об оповещении: время, имя, состояние, идентификатор и подписка. | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Дополнительная информация

- [Поддерживаемые решения SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Потоковая передача журналов действий в концентраторы событий](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Оповещения безопасности](../security-center/security-center-managing-and-responding-alerts.md)