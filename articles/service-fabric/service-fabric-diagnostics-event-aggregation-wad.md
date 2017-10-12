---
title: "Агрегация событий Azure Service Fabric c помощью системы диагностики Microsoft Azure | Документация Майкрософт"
description: "Ознакомьтесь со сведениями об агрегации и сборе событий с использованием WAD для мониторинга и диагностики кластеров Azure Service Fabric."
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
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 5773361fdec4cb8ee54fa2856f6aa969d5dac4e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Агрегирование и сбор событий с помощью системы диагностики Microsoft Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Централизованное хранение журналов упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере.

Один из способов отправки и сбора журналов заключается в использовании расширения системы диагностики Microsoft Azure (WAD), которое отправляет журналы в службу хранилища Azure, а также может отправлять журналы в Azure Application Insights или концентраторы событий Azure. Вы также можете использовать внешний процесс, чтобы считывать события из хранилища и помещать их на платформу обработки, например в [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) или другое решение для анализа журналов.

## <a name="prerequisites"></a>Предварительные требования
Эти инструменты используются для некоторых операций в данном документе:

* [Система диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (статья посвящена облачным службам Azure, но содержит нужную информацию и примеры)
* [Диспетчер ресурсов Azure](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Клиент Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Шаблон Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Источники журналов и событий

### <a name="service-fabric-platform-events"></a>События платформы Service Fabric
Как упоминалось в [этой статье](service-fabric-diagnostics-event-generation-infra.md), Service Fabric настраивает несколько стандартных каналов ведения журнала. Следующие каналы легко настраиваются с помощью WAD для отправки данных мониторинга и диагностики в таблицу хранилища или в другое место:
  * Рабочие события: операции более высокого уровня, выполняемые платформой Service Fabric. Некоторые примеры: создание приложений и служб, изменение состояния узлов и сведения об обновлении. Они передаются в рамках журнала трассировки событий для Windows (ETW).
  * [События модели программирования на основе Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
  * [События модели программирования на основе Reliable Services](service-fabric-reliable-services-diagnostics.md).

### <a name="application-events"></a>События приложения
 События, которые генерируются кодом служб и приложений и записываются с помощью вспомогательного класса EventSource, предоставленного в шаблонах Visual Studio. Дополнительные сведения о способах записи журналов EventSource из приложения см. в статье [Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Развертывание расширения системы диагностики
Первым шагом при сборе журналов является развертывание расширения системы диагностики на каждой виртуальной машине в кластере Service Fabric. Расширение системы диагностики собирает журналы на каждой виртуальной машине и отправляет их в указанную учетную запись хранения. Действия могут немного отличаться в зависимости от того, что вы используете — портал Azure или Azure Resource Manager. Кроме того, они зависят о того, как выполняется развертывание — в ходе создания кластера или для уже существующего кластера. Рассмотрим действия для каждого сценария.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Развертывание расширения системы диагностики в ходе создания кластера с помощью портала Azure
Для развертывания расширения системы диагностики на виртуальных машинах в кластере во время его создания используется панель "Параметры диагностики", показанная на рисунке ниже. Убедитесь, что для параметра "Диагностика" установлено значение **ВКЛ** (параметр по умолчанию). После создания кластера эти параметры нельзя изменить на портале.

![Параметры системы диагностики Azure на портале для создания кластера](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

При создании кластера с помощью портала настоятельно рекомендуется скачать шаблон перед тем, как нажать кнопку **ОК** для создания кластера. Дополнительную информацию см. в статье [Создание кластера Service Fabric с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Шаблон вам понадобится позже для внесения изменений, так как не все изменения можно выполнять с помощью портала.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Развертывание расширения системы диагностики в ходе создания кластера с помощью диспетчера ресурсов Azure
Чтобы создать кластер с помощью диспетчера ресурсов, добавьте JSON-файл конфигурации системы диагностики в полный шаблон Resource Manager ресурсов кластера перед созданием кластера. Мы предоставляем пример шаблона диспетчера ресурсов для кластера из пяти виртуальных машин с конфигурацией системы диагностики (эта конфигурация входит в примеры шаблонов диспетчера ресурсов). Этот шаблон можно найти в коллекции примеров Azure. См. статью [Пример шаблона Resource Manager — кластер из пяти узлов с системой диагностики](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Чтобы просмотреть параметр системы диагностики в шаблоне Resource Manager, откройте файл azuredeploy.json и выполните поиск **IaaSDiagnostics**. Чтобы создать кластер с помощью этого шаблона, нажмите кнопку **Развернуть в Azure**, которая доступна по ссылке выше.

Также можно скачать пример шаблона Resource Manager, внести в него изменения и создать кластер на основе измененного шаблона с помощью команды `New-AzureRmResourceGroupDeployment` в окне Azure PowerShell. Параметры, передаваемые в команду, приведены в коде ниже. Дополнительные инструкции по развертыванию группы ресурсов с помощью PowerShell см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Развертывание расширения системы диагностики в существующем кластере
Если у вас есть кластер, в котором не развернута система диагностики, или вы хотите изменить существующую конфигурацию, систему диагностики можно добавить или обновить. Измените шаблон Resource Manager, который используется для создания существующего кластера, или скачайте шаблон на портале, как описано выше. Измените файл template.json, выполнив следующие действия.

Добавьте новый ресурс хранилища в шаблон, внеся изменения в раздел resources.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Затем добавьте этот ресурс в раздел parameters сразу после определений учетной записи хранения между `supportLogStorageAccountName` и `vmNodeType0Name`. Замените текст заполнителя *storage account name goes here* именем нужной учетной записи хранения.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Затем добавьте в раздел `VirtualMachineProfile` файла template.json следующий код в массиве extensions. Обязательно добавьте запятую в конце или в начале в зависимости от места вставки.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
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
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

После изменения файла template.json (как описано выше) повторно опубликуйте шаблон Resource Manager. Если шаблон экспортирован, для повторной публикации шаблона выполните файл deploy.ps1. После развертывания убедитесь, что параметр **ProvisioningState** имеет значение **Succeeded**.

## <a name="collect-health-and-load-events"></a>Сбор событий работоспособности и нагрузки

Начиная с выпуска Service Fabric 5.4 для сбора доступны события метрик работоспособности и нагрузки. Это события, создаваемые системой или кодом с помощью интерфейсов API для создания отчетов о работоспособности или нагрузке, в том числе [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) или [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Это обеспечивает статистическую обработку и просмотр состояния работоспособности системы с течением временем, а также оповещение на основе событий работоспособности или нагрузки. Чтобы просмотреть эти события в окне просмотра событий диагностики Visual Studio, добавьте Microsoft-ServiceFabric:4:0x4000000000000008 в список поставщиков трассировки событий Windows.

Чтобы включить сбор событий, измените шаблон Resource Manager, добавив в него следующее.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>Сбор событий обратного прокси-сервера

Начиная с выпуска Service Fabric 5.7 для сбора доступны события [обратного прокси-сервера](service-fabric-reverseproxy.md).
Обратный прокси-сервер передает события в два канала. Один из них содержит события ошибок, отражающие сбои обработки запросов, а другой содержит подробные события всех запросов, обработанных на обратном прокси-сервере. 

1. Сбор событий ошибок. Чтобы просмотреть эти события в окне просмотра событий диагностики Visual Studio, добавьте Microsoft-ServiceFabric:4:0x4000000000000010 в список поставщиков трассировки событий Windows.
Чтобы включить сбор событий из кластеров Azure, измените шаблон Resource Manager, добавив в него следующее.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

2. Сбор всех событий обработки запросов. В средстве просмотра событий диагностики Visual Studio измените запись Microsoft-ServiceFabric в списке поставщиков трассировки событий Windows, указав Microsoft-ServiceFabric:4:0x4000000000000020.
Для кластеров Azure Service Fabric измените шаблон Resource Manager, добавив в него следующее.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```
> Рекомендуется с осторожностью включать сбор событий из этого канала, так как это означает сбор всего трафика, проходящего через обратный прокси-сервер, что может привести к быстрому потреблению емкости хранилища.

События на всех узлах кластеров Azure Service Fabric собираются и объединяются в SystemEventTable.
Подробные сведения об устранении неполадок, связанных с событиями обратного прокси-сервера, приведены в [руководстве по диагностике обратного прокси-сервера](service-fabric-reverse-proxy-diagnostics.md).

## <a name="collect-from-new-eventsource-channels"></a>Сбор из новых каналов EventSource

Чтобы обновить службу диагностики для сбора журналов из новых каналов EventSource, представляющих новое приложение, которое вы собираетесь развернуть, выполните шаги, описанные выше, чтобы настроить службу диагностики для имеющегося кластера.

В раздел `EtwEventSourceProviderConfiguration` файла template.json нужно добавить записи для новых каналов EventSource. Это следует сделать до обновления конфигурации с помощью команды PowerShell `New-AzureRmResourceGroupDeployment`. Имя источника события определяется как часть кода в файле ServiceEventSource.cs, созданном в Visual Studio.

Например, если источнику события задано имя My-Eventsource, добавьте следующий код для помещения событий из источника My-Eventsource в таблицу MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Чтобы собрать данные счетчиков производительности и журналов событий, измените шаблон Resource Manager, используя примеры в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Затем опубликуйте шаблон Resource Manager.

## <a name="collect-performance-counters"></a>Сбор данных счетчиков производительности

Для сбора метрик производительности из кластера добавьте счетчики производительности в элемент WadCfg > DiagnosticMonitorConfiguration в шаблоне Resource Manager для кластера. Дополнительные сведения о счетчиках производительности Service Fabric, которые мы рекомендуем собирать, см. в [этой статье](service-fabric-diagnostics-event-generation-perf.md).

К примеру, установим один счетчик производительности, который будет делать выборку каждые 15 секунд (это можно изменить и указывается в формате "РТ\<время>\<единица_измерения>", например для РТ3М выборка будет выполняться каждые три минуты) и переносить соответствующие данные в требуемую таблицу хранилища каждую минуту.

  ```json
  "PerformanceCounters": {
      "scheduledTransferPeriod": "PT1M",
      "PerformanceCounterConfiguration": [
          {
              "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
              "sampleRate": "PT15S",
              "unit": "Percent",
              "annotation": [
              ],
              "sinks": ""
          }
      ]
  }
  ```
  
Если вы используете приемник Application Insights, как описано в разделе ниже, и вам нужно, чтобы эти метрики отображались в Application Insights, добавьте имя приемника в соответствующем разделе, как показано выше. Кроме того, создайте отдельную таблицу для отправки счетчиков производительности, чтобы они не вытесняли данные, поступающие с других включенных каналов ведения журналов.


## <a name="send-logs-to-application-insights"></a>Отправка журналов в Application Insights

Отправку данных мониторинга и диагностики в Application Insights можно выполнить в ходе настройки WAD. Дополнительные сведения об использовании Application Insights для анализа событий и визуализации, а также установке приемника Application Insights как части WadCfg см. в [этой статье](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="next-steps"></a>Дальнейшие действия

Если вы правильно настроили диагностику Azure, данные из журнала трассировки событий Windows и журнала EventSource станут появляться в таблице хранилища. При использовании OMS, Kibana или любой другой платформы для аналитики и визуализации, которая не настроена в шаблоне Resource Manager явным образом, убедитесь, что выбранная платформа настроена для чтения данных из таблиц хранилища. Сделать это для OMS несложно. Дополнительные сведения см. в статье [Event and log analysis through OMS](service-fabric-diagnostics-event-analysis-oms.md) (Анализ событий и журналов с помощью OMS). В этом смысле Application Insights — это особый случай, так как это решение можно настроить при настройке расширения диагностики. Дополнительные сведения об Application Insights см. в [этой статье](service-fabric-diagnostics-event-analysis-appinsights.md).

>[!NOTE]
>Сегодня не существует способа фильтрации или очистки событий, которые отправляются в таблицу. Если не реализовать метод удаления событий из таблицы, она продолжит расти. Сейчас есть пример службы очистки данных, выполняющийся в [примере модуля наблюдения](https://github.com/Azure-Samples/service-fabric-watchdog-service). Мы также советуем написать собственный пример, если у вас нет веских причин для хранения журналов дольше 30 или 90 дней.

* [Узнайте, как собирать данные счетчиков производительности или журналы, используя расширения системы диагностики.](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Анализ событий и визуализация с помощью Application Insights)
* [Event Analysis and Visualization with OMS](service-fabric-diagnostics-event-analysis-oms.md) (Анализ событий и журналов с помощью OMS)