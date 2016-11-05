---
title: Сбор журналов с помощью системы диагностики Azure | Microsoft Docs
description: В этой статье описывается, как настроить систему диагностики Azure для сбора журналов из кластера Service Fabric, запущенного в Azure.
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2016
ms.author: toddabel

---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Сбор журналов с помощью системы диагностики Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Централизованное хранение журналов упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере.

Для отправки и сбора журналов рекомендуется использовать расширение системы диагностики Azure, которое отправляет журналы в службу хранилища Azure. Журналы не используются непосредственно в хранилище. Но вы можете использовать внешний процесс, чтобы считать события из хранилища и поместить их, например, в [Log Analytics](../log-analytics/log-analytics-service-fabric.md), [ElasticSearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) или другое решение для анализа журналов.

## <a name="prerequisites"></a>Предварительные требования
Указанные далее инструменты будут использоваться для выполнения некоторых операций в данном документе.

* [Система диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (статья посвящена облачным службам Azure, но содержит нужную информацию и примеры)
* [Диспетчер ресурсов Azure](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Клиент Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Шаблон Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="log-sources-that-you-might-want-to-collect"></a>Источники журналов, которые вы можете собирать
* **Журналы Service Fabric**. Генерируются из платформы в стандартные каналы трассировки событий Windows (ETW) и EventSource. Журналы могут принадлежать к одному из следующих типов.
  * Рабочие события. Это журналы операций, выполняемых платформой Service Fabric. Некоторые примеры: создание приложений и служб, изменение состояния узлов и сведения об обновлении.
  * [События модели программирования на основе Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
  * [События модели программирования на основе Reliable Services](service-fabric-reliable-services-diagnostics.md).
* **События приложения**. Это события, которые генерируются кодом служб и записываются с помощью вспомогательного класса EventSource, предоставленного в шаблонах Visual Studio. Дополнительные сведения о способах записи журналов из приложения см. в статье [Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Развертывание расширения системы диагностики
Первым шагом при сборе журналов является развертывание расширения системы диагностики на каждой виртуальной машине в кластере Service Fabric. Расширение системы диагностики собирает журналы на каждой виртуальной машине и отправляет их в указанную учетную запись хранения. Действия могут немного отличаться в зависимости от того, что вы используете — портал Azure или Azure Resource Manager. Кроме того, они зависят о того, как выполняется развертывание — в ходе создания кластера или для уже существующего кластера. Рассмотрим действия для каждого сценария.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Развертывание расширения системы диагностики в ходе создания кластера с помощью портала
Для развертывания расширения системы диагностики на виртуальных машинах в кластере во время его создания используется панель "Параметры диагностики", показанная на рисунке ниже. Чтобы обеспечить сбор данных событий Reliable Actors или Reliable Services, убедитесь, что для системы диагностики установлено значение **Включено**, которое является значением по умолчанию. После создания кластера эти параметры нельзя изменить на портале.

![Параметры системы диагностики Azure на портале для создания кластера](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Журналы поддержки *необходимы* группе поддержки Azure при рассмотрении ваших запросов на поддержку. Эти журналы собираются в режиме реального времени и сохраняются в одной из учетных записей хранения, созданных в группе ресурсов. Параметры системы диагностики позволяют настраивать события уровня приложения. В их число входят события [Reliable Actors](service-fabric-reliable-actors-diagnostics.md), события [Reliable Services](service-fabric-reliable-services-diagnostics.md) и некоторые события Service Fabric системного уровня, которые хранятся в хранилище Azure.

С помощью таких продуктов, как [Elasticsearch](service-fabric-diagnostic-how-to-use-elasticsearch.md), или собственного процесса можно получать события из учетной записи хранения. Сегодня не существует способа фильтрации или очистки событий, которые отправляются в таблицу. Если не реализовать метод удаления событий из таблицы, она продолжит расти.

При создании кластера с помощью портала настоятельно рекомендуется скачать шаблон *перед тем, как нажать кнопку **ОК*** для создания кластера. Дополнительную информацию см. в статье [Создание кластера Service Fabric с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Шаблон вам понадобится позже для внесения изменений, так как не все изменения можно выполнять с помощью портала.

Шаблоны можно экспортировать с портала, используя приведенные ниже инструкции. Но, возможно, эти шаблоны будет сложнее использовать, так как они могут содержать несколько значений NULL, для которых необходимо будет указать значения, или в них могут отсутствовать все необходимые сведения.

1. Откройте группу ресурсов.
2. Выберите **Параметры**, чтобы отобразить панель "Параметры".
3. Щелкните **Развертывания**, чтобы отобразить панель "История развертывания".
4. Выберите определенное развертывание, чтобы отобразить подробные сведения о нем.
5. Щелкните **Экспортировать шаблон**, чтобы отобразить панель "Шаблон".
6. Щелкните **Сохранить в файл**, чтобы экспортировать ZIP-файл, содержащий шаблон, а также файлы параметров и PowerShell.

После экспорта файлов необходимо внести изменения. Отредактируйте файл parameters.json и удалите элемент **adminPassword**. Таким образом, при запуске скрипта развертывания будет запрашиваться пароль. При выполнении сценария развертывания может потребоваться исправить значения параметров NULL.

Обновление конфигурации с помощью скачанного шаблона

1. Извлеките содержимое файла в папку на локальном компьютере.
2. Измените это содержимое в соответствии с новой конфигурацией.
3. Запустите PowerShell и перейдите в папку, в которую было извлечено содержимое.
4. Запустите шаблон **deploy.ps1** и введите идентификатор подписки, имя группы ресурсов (используйте то же самое имя, чтобы обновить конфигурацию) и уникальное имя развертывания.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Развертывание расширения системы диагностики в ходе создания кластера с помощью диспетчера ресурсов Azure
Чтобы создать кластер с помощью диспетчера ресурсов, добавьте JSON-файл конфигурации системы диагностики в полный шаблон Resource Manager ресурсов кластера перед созданием кластера. Мы предоставляем пример шаблона диспетчера ресурсов для кластера из пяти виртуальных машин с конфигурацией системы диагностики (эта конфигурация входит в примеры шаблонов диспетчера ресурсов). Этот шаблон можно найти в коллекции примеров Azure. См. статью [Пример шаблона Resource Manager — кластер из пяти узлов с системой диагностики](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Чтобы просмотреть параметр системы диагностики в шаблоне Resource Manager, откройте файл azuredeploy.json и выполните поиск **IaaSDiagnostics**. Чтобы создать кластер с помощью этого шаблона, нажмите кнопку **Развернуть в Azure**, которая доступна по ссылке выше.

Также можно скачать пример шаблона Resource Manager, внести в него изменения и создать кластер на основе измененного шаблона с помощью команды `New-AzureRmResourceGroupDeployment` в окне Azure PowerShell. Параметры, передаваемые в команду, приведены в коде ниже. Дополнительные инструкции по развертыванию группы ресурсов с помощью PowerShell см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

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

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Обновление службы диагностики для сбора и отправки журналов из новых каналов EventSource
Чтобы обновить службу диагностики для сбора журналов из новых каналов EventSource, представляющих новое приложение, которое вы собираетесь развернуть, выполните шаги из [предыдущего раздела](#deploywadarm), в которых описана настройка службы диагностики для существующего кластера.

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

Чтобы собрать данные счетчиков производительности и журналов событий, измените шаблон Resource Manager, используя примеры в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Затем опубликуйте шаблон Resource Manager.

## <a name="next-steps"></a>Дальнейшие действия
Чтобы лучше понять, на какие события необходимо обращать внимание во время устранения неполадок, ознакомьтесь с диагностическими событиями, которые создаются для [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) и [Reliable Services](service-fabric-reliable-services-diagnostics.md).

## <a name="related-articles"></a>Связанные статьи
* [Узнайте, как собирать данные счетчиков производительности или журналы, используя расширения системы диагностики.](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Service Fabric Solution in Log Analytics](../log-analytics/log-analytics-service-fabric.md) (Решение Service Fabric в Log Analytics)

<!--HONumber=Oct16_HO2-->


