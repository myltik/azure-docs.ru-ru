---
title: Azure Service Fabric. Мониторинг производительности с помощью расширения для системы диагностики Microsoft Azure | Документация Майкрософт
description: Использование системы диагностики Microsoft Azure для сбора данных счетчиков производительности для кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: srrengar
ms.openlocfilehash: 5c8c1f107e9e70e72c48ea93ef211b2a760ffb5c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213237"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Мониторинг производительности с помощью расширения для системы диагностики Microsoft Azure

В этом документе рассматриваются шаги, необходимые для настройки сбора данных счетчиков производительности через расширение системы диагностики Microsoft Azure для кластеров Windows. Для кластеров Linux настройте [агент OMS](service-fabric-diagnostics-oms-agent.md), чтобы собрать данные счетчиков производительности для узлов. 

 > [!NOTE]
> Чтобы выполнение этих шагов было эффективным, на кластере необходимо развернуть расширение системы диагностики Microsoft Azure. Если оно не настроено, перейдите к разделу [Агрегирование и сбор событий с помощью системы диагностики Microsoft Azure](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

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

3. Добавьте счетчики производительности, данные которых необходимо собрать, в параметр `PerformanceCounterConfiguration`, объявленный на предыдущем шаге. Каждый счетчик, данные которого необходимо собирать, определен с помощью параметров `counterSpecifier`, `sampleRate`, `unit`, `annotation` и любых соответствующих приемников `sinks`.

Ниже приведен пример конфигурации со счетчиком для *общей загруженности процессора* (периода, в течение которого ЦП был задействован для операций обработки) и *вызовов методов субъекта Service Fabric в секунду* — одним из настраиваемых счетчиков производительности Service Fabric. Полный список настраиваемых счетчиков производительности Service Fabric см. в разделах о [счетчиках производительности Reliable Actors](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) и [счетчиках производительности Reliable Services](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
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
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Частоту выборки счетчика можно изменить согласно вашим потребностям. Допустимый формат — `PT<time><unit>`, поэтому если необходимо собирать данные счетчика каждую секунду, то следует задать значение `"sampleRate": "PT15S"`.

 >[!NOTE]
 >Несмотря на то что для указания групп счетчиков производительности с похожими именами можно использовать `*`, для отправки счетчиков через приемник (в Application Insights) требуется объявить их по отдельности. 

4. Когда вы добавите соответствующие счетчики производительности, данные которых необходимо собрать, потребуется обновить ресурс кластера, чтобы эти изменения отразились в работающем кластере. Сохраните измененный файл `template.json` и откройте PowerShell. Обновление кластера можно выполнить с помощью `New-AzureRmResourceGroupDeployment`. Для вызова необходимо указать имя группы ресурсов, обновленный файл шаблона и файл параметров. Кроме того, вызов отправляет запрос в Resource Manager на внесение соответствующих изменений в обновленные ресурсы. После входа в учетную запись и правильную подписку используйте следующую команду для выполнения обновления:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. По завершении обновления (оно занимает 15–45 минут) система диагностики Microsoft Azure должна собирать данные счетчиков производительности и отправлять их в таблицу с именем WADPerformanceCountersTable в учетной записи хранения, связанной с вашим кластером. Просматривайте данные счетчиков производительности в Application Insights, [добавив приемник AI в шаблон Resource Manager](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Дополнительная информация
* Собирайте данные дополнительных счетчиков производительности для кластера. Список счетчиков, данные которых следует собирать, см. в статье [Метрики производительности](service-fabric-diagnostics-event-generation-perf.md).
* [Используйте мониторинг и систему диагностики с виртуальной машиной Windows и шаблонами Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md), чтобы внести изменения в раздел `WadCfg`, включая настройку дополнительных учетных записей хранения для отправки данных диагностики.
