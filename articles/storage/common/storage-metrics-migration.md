---
title: Перенос метрик службы хранилища Azure | Документация Майкрософт
description: Узнайте, как преобразовать метрики прежних версий в новые метрики, управляемые Azure Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: da8eb0b9e2e5aba60b61a36d83f525c7ce4a7958
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776267"
---
# <a name="azure-storage-metrics-migration"></a>Перенос метрик службы хранилища Azure

В соответствии со стратегией унификации работы с Monitor в Azure служба хранилища Azure выставляет метрики на платформе Azure Monitor. В будущем предоставление службы поддержки метрик прежних версий будет завершено с предварительным извещением на основе политики Azure. Если вы используете метрики службы хранилища прежних версий, необходимо выполнить перенос до даты окончания действия службы, чтобы сохранить данные метрик.

В этой статье описывается переход с метрик прежних версий на новые метрики.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Основные сведения о метриках прежних версий, управляемых службой хранилища Azure

Служба хранилища Azure собирает метрики прежних версий, выполняет статистические вычисления и сохраняет их в таблицах $Metric в одной учетной записи хранения. С помощью портала Azure можно настроить диаграмму мониторинга. Кроме того, с помощью пакетов SDK службы хранилища Azure можно читать данные из таблиц $Metric на основе схемы. Дополнительные сведения можно найти в этом разделе об [Аналитике Службы хранилища](./storage-analytics.md).

Метрики прежних версий обеспечивают только данные о емкости больших двоичных объектов и данные о транзакциях с большими двоичными объектами, таблицами, файлами и очередями.

Метрики прежних версий разработаны на основе неструктурированной схемы. Поэтому при отсутствии шаблонов трафика для активации метрики ее данные отсутствовали. Например, **ServerTimeoutError** имеет значение 0 в таблицах $Metric даже в том случае, если вы не получаете какие-либо ошибки времени ожидания сервера из реального трафика для учетной записи хранения.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Основные сведения о новых метриках, управляемых Azure Monitor

Данные новых метрик служба хранилища Azure передает в серверную часть Azure Monitor. Azure Monitor обеспечивает унифицированный мониторинг, включая данные с портала и прием данных. Дополнительные сведения можно найти в этой [статье](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Новые метрики обеспечивают данные о емкости и транзакциях для больших двоичных объектов, таблиц, файлов, очередей и хранилища уровня "Премиум".

Многомерность является одной из функций Azure Monitor. Служба хранилища Azure учитывает это при определении новой схемы метрики. Дополнительные сведения о поддерживаемых измерениях метрик можно найти в этой статье о [метриках службы хранилища Azure в Azure Monitor](./storage-metrics-in-azure-monitor.md). Многомерность обеспечивает экономичность в использовании пропускной способности при приеме и емкости при хранении данных метрик. Следовательно, если трафик не активировал связанные метрики, то их данные не создаются. Например, если трафик не активировал какие-либо ошибки времени ожидания сервера, то Azure Monitor не возвращает никаких данных при запросе значения **Transactions** метрики с измерением **ResponseType**, равным **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Сопоставление метрик прежних версий и новых метрик

При программном чтении данных метрик необходимо перейти на новую схему метрики в своих программах. Чтобы лучше понять изменения, можно ознакомиться с сопоставлением в следующей таблице.

**Метрики емкости**

| Метрика прежних версий | Новая метрика |
| ------------------- | ----------------- |
| Capacity            | BlobCapacity с измерением BlobType, равным BlockBlob или PageBlob. |
| ObjectCount         | BlobCount с измерением BlobType, равным BlockBlob или PageBlob. |
| ContainerCount      | ContainerCount |

Следующие метрики представляют собой новые предложения, не поддерживаемые метриками прежних версий.
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Метрики транзакций**

| Метрика прежних версий | Новая метрика |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transactions с измерением ResponseType, равным AuthorizationError. |
| AnonymousClientOtherError | Transactions с измерением ResponseType, равным ClientOtherError. |
| AnonymousClientTimeoutError | Transactions с измерением ResponseType, равным ClientTimeoutError. |
| AnonymousNetworkError | Transactions с измерением ResponseType, равным NetworkError. |
| AnonymousServerOtherError | Transactions с измерением ResponseType, равным ServerOtherError. |
| AnonymousServerTimeoutError | Transactions с измерением ResponseType, равным ServerTimeoutError. |
| AnonymousSuccess | Transactions с измерением ResponseType, равным Success. |
| AnonymousThrottlingError | Transactions с измерением ResponseType, равным ClientThrottlingError или ServerBusyError. |
| AuthorizationError | Transactions с измерением ResponseType, равным AuthorizationError. |
| Доступность | Доступность |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transactions с измерением ResponseType, равным ClientOtherError. |
| ClientTimeoutError | Transactions с измерением ResponseType, равным ClientTimeoutError. |
| NetworkError | Transactions с измерением ResponseType, равным NetworkError. |
| PercentAuthorizationError | Transactions с измерением ResponseType, равным AuthorizationError. |
| PercentClientOtherError | Transactions с измерением ResponseType, равным ClientOtherError. |
| PercentNetworkError | Transactions с измерением ResponseType, равным NetworkError. |
| PercentServerOtherError | Transactions с измерением ResponseType, равным ServerOtherError. |
| PercentSuccess | Transactions с измерением ResponseType, равным Success. |
| PercentThrottlingError | Transactions с измерением ResponseType, равным ClientThrottlingError или ServerBusyError. |
| PercentTimeoutError | Transactions с измерением ResponseType, равным ServerTimeoutError, или с измерением ResponseType, равным ClientTimeoutError.|
| SASAuthorizationError | Transactions с измерением ResponseType, равным AuthorizationError. |
| SASClientOtherError | Transactions с измерением ResponseType, равным ClientOtherError. |
| SASClientTimeoutError | Transactions с измерением ResponseType, равным ClientTimeoutError. |
| SASNetworkError | Transactions с измерением ResponseType, равным NetworkError. |
| SASServerOtherError | Transactions с измерением ResponseType, равным ServerOtherError. |
| SASServerTimeoutError | Transactions с измерением ResponseType, равным ServerTimeoutError. |
| SASSuccess | Transactions с измерением ResponseType, равным Success. |
| SASThrottlingError | Transactions с измерением ResponseType, равным ClientThrottlingError или ServerBusyError. |
| ServerOtherError | Transactions с измерением ResponseType, равным ServerOtherError. |
| ServerTimeoutError | Transactions с измерением ResponseType, равным ServerTimeoutError. |
| Успешно | Transactions с измерением ResponseType, равным Success. |
| ThrottlingError | Transactions с измерением ResponseType, равным ClientThrottlingError или ServerBusyError. |
| TotalBillableRequests | Транзакции |
| TotalEgress | Исходящие |
| TotalIngress | Входящие |
| TotalRequests | Транзакции |

## <a name="faq"></a>Часто задаваемые вопросы

* Как перенести существующие правила генерации оповещений?

Ответ. Если вы создали классические правила генерации оповещений, основанные на метриках службы хранилища прежних версий, необходимо создать новые правила генерации оповещений, основанные на новой схеме метрики.

* Будет ли данные новых метрик по умолчанию сохраняться в ту же учетную запись хранения?

О. Нет. Если необходимо архивировать данные метрик в учетную запись хранения, можно использовать [API настройки диагностики Azure Monitor](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Дополнительная информация

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Метрики службы хранилища в Azure Monitor](./storage-metrics-in-azure-monitor.md)
