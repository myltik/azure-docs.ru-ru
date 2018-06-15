---
title: Мониторинг контейнеров в службе "Экземпляры контейнеров Azure"
description: Сведения о том, как отслеживать потребление контейнерами вычислительных ресурсов, таких как ЦП и память, в службе "Экземпляры контейнеров Azure".
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: nepeters
ms.openlocfilehash: 814346bd8021b996b64cd7f0311f31b13b32a8c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180408"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Мониторинг ресурсов контейнеров в службе "Экземпляры контейнеров Azure"

Azure Monitor предоставляет информацию о том, какие вычислительные ресурсы используют экземпляры контейнеров. Используйте Azure Monitor, чтобы контролировать потребление ресурсов ЦП и памяти для групп контейнеров и отдельных контейнеров. Эти данные об использовании ресурсов помогут определить оптимальные настройки ЦП и памяти для конкретных групп контейнеров.

В этом документе описывается, как происходит сбор данных о потреблении ЦП и памяти для экземпляров контейнеров с помощью портала Azure и Azure CLI.

> [!IMPORTANT]
> Сейчас метрики потребления ресурсов доступны только для контейнеров Linux.
>

## <a name="available-metrics"></a>Доступные метрики

Azure Monitor предоставляет метрики по потреблению **ЦП** и **памяти** для службы "Экземпляры контейнеров Azure". Обе эти метрики доступны как для отдельных контейнеров, так и для групп контейнеров.

Метрики ЦП измеряются в **миллиядрах**. Одно миллиядро равно 0,001 используемого ядра ЦП. Таким образом, 500 миллиядер соответствует загрузке одного ядра на 50 %.

Метрики памяти измеряются в **байтах**.

## <a name="get-metrics---azure-portal"></a>Получение метрик на портале Azure

При создании группы контейнеров в Azure Monitor данные отображаются на портале Azure. Чтобы увидеть метрики для группы контейнеров, выберите нужную группу ресурсов, а затем группу контейнеров. Здесь вы видите предварительно созданные диаграммы по потреблению ЦП и памяти.

![Сдвоенные диаграммы][dual-chart]

Если ваша группа контейнеров содержит несколько контейнеров, используйте функцию [измерения][monitor-dimension], чтобы получить метрики по каждому контейнеру. Чтобы создать диаграмму с метриками по отдельному контейнеру, выполните следующие действия:

1. В меню навигации слева выберите **Мониторинг**.
2. Выберите группу контейнеров и метрику (ЦП или память).
3. Нажмите зеленую кнопку измерения, а затем выберите **Имя контейнера**.

![Измерение][dimension]

## <a name="get-metrics---azure-cli"></a>Получение метрик через Azure CLI

Также данные о потреблении ЦП и памяти по экземплярам контейнеров можно получить с помощью Azure CLI. Для начала получите идентификатор группы контейнеров, используя приведенную ниже команду. Замените `<resource-group>` именем нужной группы ресурсов, а `<container-group>` — именем группы контейнеров.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Следующая команда возвращает метрики потребления **ЦП**:

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

А эта команда возвращает метрики потребления **памяти**:

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

В группе с несколькими контейнерами можно добавить измерение `containerName`, чтобы получить данные по отдельным контейнерам.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о мониторинге в Azure вы найдете [в этой статье][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring-and-diagnostics/monitoring-metric-charts.md#what-are-multi-dimensional-metrics
