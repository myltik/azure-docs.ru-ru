---
title: Включение ведения журнала диагностики для событий пакетной службы в Azure | Документация Майкрософт
description: Запись и анализ событий журнала диагностики для ресурсов учетной записи пакетной службы Azure, таких как пулы и задачи.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4c68df9650fa300ea20ea0621c732cb96d167ef
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>События журнала для диагностики и мониторинга решений пакетной службы

Как и многие другие службы Azure, пакетная служба генерирует события журналов для некоторых ресурсов в течение их жизненного цикла. Вы можете включить журналы диагностики для пакетной службы Azure, чтобы собирать события таких ресурсов, как пулы и задачи, а затем использовать эти журналы для диагностики и мониторинга. Журналы диагностики пакетной службы содержат такие события, как создание пула, удаление пула, начало задачи, завершение задачи и т. п.

> [!NOTE]
> В этой статье описывается ведение журнала событий для ресурсов пакетной службы, а не журнала выходных данных задач и заданий. Сведения о сохранении выходных данных заданий и задач вы найдете в статье [Сохранение выходных данных заданий и задач пакетной службы Azure](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
* [Учетная запись пакетной службы Azure](batch-account-create-portal.md)
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Чтобы сохранять журналы диагностики пакетной службы, следует создать учетную запись хранения. В этом хранилище Azure будет хранить журналы. Эту учетную запись хранения вы укажете в процессе [включения ведения журналов диагностики](#enable-diagnostic-logging) для учетной записи пакетной службы. Учетная запись хранения, которую вы указываете при включении сбора журналов, не совпадает со связанной учетной записью хранения, которая упоминается в статьях о [пакетах приложений](batch-application-packages.md) и о [сохраняемости результатов выполнения задач](batch-task-output.md).
  
  > [!WARNING]
  > Вы будете **оплачивать** данные, размещенные в учетной записи хранения Azure. В том числе и журналы диагностики, о которых идет речь в этой статье. Не забывайте об этом, когда разрабатываете [политику хранения журналов](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Включение ведения журналов диагностики
Ведение журналов диагностики для учетной записи пакетной службы по умолчанию отключено. Ведение журналов диагностики следует явно включить для каждой учетной записи пакетной службы, которые нужно отслеживать.

[Как включить сбор журналов диагностики](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Мы рекомендуем полностью прочитать статью [Обзор журналов диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), чтобы не только научиться включать ведение журнала, но и узнать, какие категории журналов поддерживаются различными службами Azure. Например, пакетная служба Azure в настоящее время поддерживает только одну категорию журналов: **журналы служб**.

## <a name="service-logs"></a>Журналы служб
Журналы пакетной службы Azure содержат события, генерируемые пакетной службой Azure на всем протяжении существования ресурса пакетной службы, например пула или задачи. Каждое событие, генерируемой пакетной службой, хранится в формате JSON в указанной учетной записи хранилища. Например, так выглядит текст **события создания пула**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Содержание каждого события находится в JSON-файле, размещенном в указанной учетной записи хранения Azure. Если вам нужен прямой доступ к журналам, вы можете изучить [схему журналов диагностики в учетной записи хранилища](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>События журнала службы
В настоящее время пакетная служба генерирует следующие события журнала службы. Этот список может оказаться неполным, если с момента последнего обновления этой статьи были добавлены новые события.

| **События журнала службы** |
| --- |
| [Pool create][pool_create] |
| [Pool delete start][pool_delete_start] |
| [Pool delete complete][pool_delete_complete] |
| [Pool resize start][pool_resize_start] |
| [Pool resize complete][pool_resize_complete] |
| [Task start][task_start] |
| [Task complete][task_complete] |
| [Task fail][task_fail] |

## <a name="next-steps"></a>Дополнительная информация
Помимо хранения событий журнала диагностики в учетной записи хранения Azure вы можете настроить потоковую передачу событий журнала пакетной службы в [концентратор событий Azure](../event-hubs/event-hubs-what-is-event-hubs.md) для передачи в [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Потоковая передача журналов диагностики Azure в концентраторы событий](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Потоковая передача диагностических событий пакетной службы в концентраторы событий, которые представляют собой масштабируемую службу приема данных. Концентраторы событий способны принимать миллионы событий в секунду, позволяя преобразовать и сохранять их с помощью любого поставщика аналитики в реальном времени.
* [Анализ журналов диагностики Azure с помощью Log Analytics](../log-analytics/log-analytics-azure-storage.md)
  
  Отправьте журналы диагностики в Log Analytics, где вы сможете проанализировать их с помощью портала Azure или экспортировать для анализа в Power BI или Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
