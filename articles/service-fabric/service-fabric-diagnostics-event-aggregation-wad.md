---
title: Агрегация событий Azure Service Fabric c помощью системы диагностики Microsoft Azure | Документация Майкрософт
description: Ознакомьтесь со сведениями об агрегации и сборе событий с использованием WAD для мониторинга и диагностики кластеров Azure Service Fabric.
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
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: 38a026e8995bb7384c866dcd2f12588ca816009f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205779"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Агрегирование и сбор событий с помощью системы диагностики Microsoft Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Централизованное хранение журналов упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере.

Один из способов отправки и сбора журналов заключается в использовании расширения системы диагностики Microsoft Azure (WAD), которое отправляет журналы в службу хранилища Azure, а также может отправлять журналы в Azure Application Insights или концентраторы событий Azure. Вы также можете использовать внешний процесс, чтобы считывать события из хранилища и помещать их на платформу обработки, например в [Log Analytics](../log-analytics/log-analytics-service-fabric.md) или другое решение для анализа журналов.

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве используются инструменты, представленные ниже.

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Шаблон Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>События платформы Service Fabric
Service Fabric настраивает несколько [стандартных каналов ведения журнала](service-fabric-diagnostics-event-generation-infra.md), и для нескольких из них расширение предварительно настраивает отправку данных мониторинга и диагностики в таблицу хранилища или в другое расположение.
  * [Рабочие события](service-fabric-diagnostics-event-generation-operational.md): операции высокого уровня, выполняемые платформой Service Fabric. Некоторые примеры: создание приложений и служб, изменение состояния узлов и сведения об обновлении. Они передаются в рамках журнала трассировки событий для Windows (ETW).
  * [События модели программирования на основе Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
  * [События модели программирования на основе Reliable Services](service-fabric-reliable-services-diagnostics.md).

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Развертывание расширения системы диагностики с помощью портала
Для сбора журналов прежде всего нужно развернуть расширение системы диагностики на каждом узле масштабируемого набора виртуальных машин в кластере Service Fabric. Расширение системы диагностики собирает журналы на каждой виртуальной машине и отправляет их в указанную учетную запись хранения. Ниже описано, как настроить это для новых и существующих кластеров с помощью портала Azure и шаблонов Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Развертывание расширения системы диагностики в ходе создания кластера с помощью портала Azure
При создании кластера, на этапе конфигурации кластера, разверните дополнительные параметры и убедитесь, что здесь **включен** параметр диагностики (это значение по умолчанию).

![Параметры системы диагностики Azure на портале для создания кластера](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Мы настоятельно рекомендуем скачать шаблон **прежде, чем вы щелкнете "Создать"** на последнем шаге. Дополнительную информацию см. в статье [Создание кластера Service Fabric с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Этот шаблон потребуется для настройки каналов (перечисленных выше), из которых вы намерены собирать данные.

![Шаблон кластера](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Итак, вы настроили сбор данных о событиях в хранилище Azure. Теперь следует [настроить Log Analytics](service-fabric-diagnostics-oms-setup.md) для анализа данных и запроса информации с помощью портала Log Analytics.

>[!NOTE]
>Сейчас не поддерживается возможность фильтрации или очистки событий, которые отправляются в таблицы. Если вы не реализуете метод для удаления событий из таблицы, она будет постоянно расти. По умолчанию действует ограничение в 50 ГБ. Вы можете изменить это ограничение, используя [инструкции, приведенные ниже в этой статье](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Кроме того, там есть пример службы для очистки данных, выполняющийся в [примере модуля наблюдения](https://github.com/Azure-Samples/service-fabric-watchdog-service). Мы рекомендуем создать свою службу, если у вас нет веских причин хранить журналы дольше 30 или 90 дней.

## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Развертывание расширения системы диагностики с помощью Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Создание кластера с расширением системы диагностики
Чтобы создать кластер с помощью Resource Manager, добавьте код JSON с конфигурацией системы диагностики в полный шаблон Resource Manager перед созданием кластера. Мы предоставляем пример шаблона диспетчера ресурсов для кластера из пяти виртуальных машин с конфигурацией системы диагностики (эта конфигурация входит в примеры шаблонов диспетчера ресурсов). Этот шаблон можно найти в коллекции примеров Azure. См. статью [Пример шаблона Resource Manager — кластер из пяти узлов с системой диагностики](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Чтобы просмотреть параметр системы диагностики в шаблоне Resource Manager, откройте файл azuredeploy.json и выполните поиск **IaaSDiagnostics**. Чтобы создать кластер с помощью этого шаблона, нажмите кнопку **Развернуть в Azure**, которая доступна по ссылке выше.

Также можно скачать пример шаблона Resource Manager, внести в него изменения и создать кластер на основе измененного шаблона с помощью команды `New-AzureRmResourceGroupDeployment` в окне Azure PowerShell. Параметры, передаваемые в команду, приведены в коде ниже. Дополнительные инструкции по развертыванию группы ресурсов с помощью PowerShell см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Добавление расширения системы диагностики к существующему кластеру
Если у вас есть кластер, в котором еще не развернута система диагностики, вы можете добавить или обновить систему диагностики с помощью шаблона кластера. Измените шаблон Resource Manager, который используется для создания существующего кластера, или скачайте шаблон на портале, как описано выше. Измените файл template.json, выполнив следующие действия.

Добавьте новый ресурс хранилища в шаблон, внеся изменения в раздел resources.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Затем добавьте этот ресурс в раздел parameters сразу после определений учетной записи хранения между `supportLogStorageAccountName`. Замените текст заполнителя *storage account name goes here* именем учетной записи хранения, которую вы намерены использовать.

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
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
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

> [!TIP]
> Если планируется развернуть контейнеры в кластере, разрешите WAD получать статистику Docker, добавив в раздел **WadCfg > DiagnosticMonitorConfiguration** следующий код:
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Изменение квоты хранилища

Это расширение постоянно увеличивает размер заполняемых таблиц, пока не будет достигнута квота хранилища. Возможно, вы захотите уменьшить эту квоту. По умолчанию квота имеет значение 50 ГБ, а изменить ее можно в шаблоне, в поле `overallQuotainMB` раздела `DiagnosticMonitorConfiguration`.

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Настройка сбора журналов
Для сбора доступны журналы из нескольких дополнительных каналов, и здесь мы опишем несколько распространенных конфигураций, которые можно применить в шаблоне для кластеров, работающих в Azure.

* Операционный канал — базовые сведения (включен по умолчанию). Содержит события высокоуровневых операций, выполняемых Service Fabric и кластером, таких как открытие узла, развертывание нового приложения, откат обновления и т. д. Список поддерживаемых событий вы найдете [здесь](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Операционный канал — подробные сведения. Включает отчеты о работоспособности и решения о балансировке нагрузки, а также все содержимое операционного канала базовых сведений. Эти события создаются системой или кодом через API отчетов о работоспособности и (или) нагрузке, такие как [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) или [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Чтобы просмотреть эти события в окне просмотра событий диагностики Visual Studio, добавьте Microsoft-ServiceFabric:4:0x4000000000000008 в список поставщиков трассировки событий Windows.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Канал данных и обмена сообщениями — базовые сведения. Содержит критически важные журналы и события, создаваемые в системе обмена сообщениями (пока только ReverseProxy) и пути обработки данных, а также подробные журналы операционного канала. Эти события также включают сбои при обработке запросов и другие критические проблемы для ReverseProxy и обрабатываемых запросов. **Мы рекомендуем использовать этот уровень для ведения подробного журнала**. Чтобы просмотреть эти события в средстве просмотра диагностических событий Visual Studio, добавьте Microsoft-ServiceFabric:4:0x4000000000000010 в список поставщиков трассировки событий Windows.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Канал данных и обмена сообщениями — подробные сведения. Этот канал содержит развернутую информацию из некритических журналов для процессов обработки данных и обмена сообщениями в кластере, а также все содержимое операционного канала подробных сведений. Подробные сведения об устранении любых неполадок, связанных с событиями обратного прокси-сервера, приведены в [руководстве по диагностике обратного прокси-сервера](service-fabric-reverse-proxy-diagnostics.md).  Чтобы просмотреть эти события в средстве просмотра диагностических событий Visual Studio, добавьте Microsoft-ServiceFabric:4:0x4000000000000020 в список поставщиков трассировки событий Windows.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Этот канал содержит огромный объем сведений о событиях, поэтому включение сбора этих данных приводит к быстрому созданию множества трассировок, которые могут потреблять емкость хранилища. Включайте этот режим только при крайней необходимости.


Чтобы включить рекомендуемый режим подробного ведения журналов (**Канал данных и обмена сообщениями — базовые сведения**), используйте следующее значение для `EtwManifestProviderConfiguration` в шаблоне `WadCfg`:

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
                "scheduledTransferKeywordFilter": "4611686018427387928",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

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

Для сбора метрик производительности из кластера добавьте счетчики производительности в элемент WadCfg > DiagnosticMonitorConfiguration в шаблоне Resource Manager для кластера. Дополнительные сведения о том, как изменить `WadCfg`, чтобы собрать данные конкретных счетчиков производительности, см. в статье [Performance monitoring with Windows Azure Diagnostics extension](service-fabric-diagnostics-perf-wad.md) (Мониторинг производительности с помощью расширения системы диагностики Microsoft Azure). Список счетчиков производительности Service Fabric, которые мы рекомендуем собирать, см. в статье [Метрики производительности](service-fabric-diagnostics-event-generation-perf.md).
  
Если вы используете приемник Application Insights, как описано в разделе ниже, и вам нужно, чтобы эти метрики отображались в Application Insights, добавьте имя приемника в соответствующем разделе, как показано выше. Это позволит автоматически отправлять счетчики производительности, отдельно настроенные для ресурса Application Insights.


## <a name="send-logs-to-application-insights"></a>Отправка журналов в Application Insights

Отправку данных мониторинга и диагностики в Application Insights можно выполнить в ходе настройки WAD. Дополнительные сведения об использовании Application Insights для анализа событий и визуализации см. в [этом разделе](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Дополнительная информация

Если вы правильно настроили диагностику Azure, данные из журнала трассировки событий Windows и журнала EventSource станут появляться в таблице хранилища. При использовании Log Analytics, Kibana или любой другой платформы для аналитики и визуализации, которая не настроена в шаблоне Resource Manager явным образом, убедитесь, что выбранная платформа настроена для чтения данных из таблиц хранилища. Сделать это для Log Analytics несложно. Дополнительные сведения см. в статье об [анализе событий и журналов](service-fabric-diagnostics-event-analysis-oms.md). В этом смысле Application Insights — это особый случай, так как это решение можно настроить при настройке расширения диагностики. Дополнительные сведения об Application Insights см. в [этой статье](service-fabric-diagnostics-event-analysis-appinsights.md).

>[!NOTE]
>Сегодня не существует способа фильтрации или очистки событий, которые отправляются в таблицу. Если не реализовать метод удаления событий из таблицы, она продолжит расти. Сейчас есть пример службы очистки данных, выполняющийся в [примере модуля наблюдения](https://github.com/Azure-Samples/service-fabric-watchdog-service). Мы также советуем написать собственный пример, если у вас нет веских причин для хранения журналов дольше 30 или 90 дней.

* [Узнайте, как собирать данные счетчиков производительности или журналы, используя расширения системы диагностики.](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Анализ событий и визуализация с помощью Application Insights)
* [Анализ и визуализация событий с помощью Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
