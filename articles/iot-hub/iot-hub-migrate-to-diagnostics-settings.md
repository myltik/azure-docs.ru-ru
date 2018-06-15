---
title: Перенос Центра Интернета вещей Azure для использования параметров диагностики | Документация Майкрософт
description: Узнайте, как обновить Центр Интернета вещей Azure для использования параметров системы диагностики Azure вместо мониторинга операций для отслеживания состояния операций в Центре Интернета вещей в реальном времени.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 85bdb4b4802283c591e4d7a9e8b14ae74fa44e8d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633593"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Перенос Центра Интернета вещей для перехода с операций мониторинга к параметрам диагностики

Клиенты, использующие [мониторинг операций][lnk-opsmon] для отслеживания состояния операций Центра Интернета вещей, могут перенести этот рабочий процесс в [параметры системы диагностики Azure][lnk-diagnostics-settings], компонент Azure Monitor. Параметры диагностики предоставляют сведения о диагностике уровня ресурса для многих служб Azure.

Функции мониторинга операций Центра Интернета вещей являются устаревшими и в будущем будут удалены. В этой статье содержатся шаги по перемещению рабочих нагрузок из мониторинга операций в параметры диагностики. Дополнительные сведения о временной шкале удаления функции см. в статье [Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health][lnk-blog-announcement] (Мониторинг решений Интернета вещей Azure с помощью служб Azure Monitor и "Работоспособность ресурсов Azure").

## <a name="update-iot-hub"></a>Обновление Центра Интернета вещей

Чтобы обновить Центр Интернета вещей на портале Azure, сначала включите параметры диагностики, а затем отключите операции мониторинга.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Отключение мониторинга операций

После проверки новых параметров диагностики в рабочем процессе компонент мониторинга операций можно отключить. 

1. В меню Центра Интернета вещей выберите **Мониторинг операций**.
1. В каждой категории мониторинга выберите **Отсутствует**.
1. Сохраните изменения мониторинга операций.

## <a name="update-applications-that-use-operations-monitoring"></a>Обновления приложений, использующих мониторинг операций

Схемы для мониторинга операций и параметров диагностики будут немного отличаться. Важно обновить приложения, которые сейчас используют мониторинг операций, для сопоставления со схемой, используемой параметрами диагностики. 

Кроме того, параметры диагностики предлагают пять новых категорий для отслеживания. После обновления приложений для имеющейся схемы, добавьте также новые категории:

- Операции переноса из облака на двойник устройства
- операции переноса с двойника устройства в облако;
- запросы к двойникам;
- Операции заданий
- Прямые методы

Определенные структуры схем см. в разделе [Сведения о журналах][lnk-diagnostics-schema].

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг работоспособности Центра Интернета вещей Azure и быстрая диагностика неполадок][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/
[lnk-monitor]: iot-hub-monitor-resource-health.md
