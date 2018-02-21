---
title: "Azure Service Fabric. Мониторинг производительности с помощью расширения системы диагностики Microsoft Azure | Документация Майкрософт"
description: "Использование системы диагностики Microsoft Azure для сбора данных счетчиков производительности для кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Мониторинг производительности с помощью расширения системы диагностики Microsoft Azure

В этом документе рассматриваются шаги, необходимые для настройки сбора данных счетчиков производительности через расширение системы диагностики Microsoft Azure для кластеров Windows. Для кластеров Linux настройте [агент OMS](service-fabric-diagnostics-oms-agent.md), чтобы собрать данные счетчиков производительности для узлов. 

 > [!NOTE]
> Чтобы выполнение этих шагов было эффективным, на кластере необходимо развернуть расширение системы диагностики Microsoft Azure. Если оно не настроено, перейдите к статье [Агрегирование и сбор событий с помощью системы диагностики Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) и следуйте инструкциям в разделе *Развертывание расширения системы диагностики*.

## <a name="collect-performance-counters-via-the-wadcfg"></a>Сбор данных счетчиков производительности с помощью раздела WadCfg

Для сбора данных счетчиков производительности через систему диагностики Microsoft Azure необходимо изменить конфигурацию соответствующим образом в шаблоне Resource Manager кластера. Выполните эти шаги, чтобы добавить счетчик производительности, с которого нужно собирать данные, в шаблон и запустить обновление ресурса Resource Manager.

1. Найдите конфигурацию системы диагностики Microsoft Azure в шаблоне кластера, а именно раздел `WadCfg`. Вы добавите счетчики производительности для сбора в разделе `DiagnosticMonitorConfiguration`.

2. Настройте конфигурацию для сбора данных счетчиков производительности, добавив следующий раздел в `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` определяет частоту передачи собираемых значений счетчиков в таблицу хранилища Azure и в любой настроенный приемник. 

3. Добавьте счетчики производительности, данные которых необходимо собрать, в параметр `PerformanceCounterConfiguration`, объявленный на предыдущем шаге. Каждый счетчик, данные которого необходимо собирать, определен с помощью параметров `counterSpecifier`, `sampleRate`, `unit`, `annotation` и любых соответствующих приемников `sinks`. Ниже приведен пример счетчика производительности для *общей загруженности процессора* (промежуток времени использования ЦП для операций обработки):

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
            {
                "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                "sampleRate": "PT1M",
                "unit": "Percent",
                "annotation": [
                ],
                "sinks": ""
            }
        ]
    }
    ```

    Частоту выборки счетчика можно изменить согласно вашим потребностям. Допустимый формат — `PT<time><unit>`, поэтому если необходимо собирать данные счетчика каждую секунду, то следует задать значение `"sampleRate": "PT15S"`.

    Аналогичным образом можно собирать данные нескольких счетчиков производительности, добавив их в список в `PerformanceCounterConfiguration`. Несмотря на то что для указания групп счетчиков производительности с похожими именами можно использовать `*`, для отправки счетчиков через приемник (в Application Insights) требуется объявить их по отдельности. 

4. После добавления соответствующих счетчиков производительности, данные которых необходимо собрать, вам потребуется обновить ресурс кластера, чтобы эти изменения отразились в работающем кластере. Сохраните измененный файл `template.json` и откройте PowerShell. Обновление кластера можно выполнить с помощью `New-AzureRmResourceGroupDeployment`. Для вызова необходимо указать имя группы ресурсов, обновленный файл шаблона и файл параметров. Кроме того, вызов отправляет запрос в Resource Manager на внесение соответствующих изменений в обновленные ресурсы. После входа в учетную запись и правильную подписку используйте следующую команду для выполнения обновления:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. После завершения обновления (занимает 15–45 минут) система диагностики Microsoft Azure должна собирать данные счетчиков производительности и отправлять их в таблицу в учетной записи хранения, объявленную в `WadCfg`.

## <a name="next-steps"></a>Дополнительная информация
* Просматривайте данные счетчиков производительности в Application Insights путем добавления приемника Application Insights в раздел `WadCfg`. Сведения о настройке приемника Application Insights см. в разделе *Добавление приемника Application Insights в шаблон Resource Manager* статьи [Анализ и визуализация событий с помощью Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
* Собирайте данные дополнительных счетчиков производительности для кластера. Список счетчиков, данные которых следует собирать, см. в статье [Метрики производительности](service-fabric-diagnostics-event-generation-perf.md).
* [Используйте мониторинг и систему диагностики с виртуальной машиной Windows и шаблонами Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md), чтобы внести изменения в раздел `WadCfg`, включая настройку дополнительных учетных записей хранения для отправки данных диагностики.