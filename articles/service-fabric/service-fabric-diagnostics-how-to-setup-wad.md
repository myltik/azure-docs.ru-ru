<properties
   pageTitle="Сбор журналов с помощью системы диагностики Azure | Microsoft Azure"
   description="В этой статье описывается, как настроить систему диагностики Azure для сбора журналов из кластера Service Fabric, запущенного в Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="toddabel"/>


# Сбор журналов с помощью системы диагностики Azure

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Если все журналы хранятся централизованно, это упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере. Для отправки и сбора журналов рекомендуется использовать расширение системы диагностики Azure, которое отправляет журналы в службу хранилища Azure. Эти журналы не играют существенной роли внутри хранилища, но с помощью внешнего средства из хранилища можно считать события и поместить их в такое средство, как [Operational Insights](https://azure.microsoft.com/services/operational-insights/), Elastic Search и т. п.

## Предварительные требования
Эти инструменты будут использоваться для некоторых операций в данном документе:

* [Система диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (статья посвящена облачным службам Azure, но содержит нужную информацию и примеры).
* [Диспетчер ресурсов Azure](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Клиент Azure Resource Manager](https://github.com/projectkudu/ARMClient)

## Различные источники журналов, которые вы можете собирать
1. **Журналы Service Fabric**. Генерируются платформой в стандартные каналы трассировки событий Windows (ETW) и EventSource. Журналы могут принадлежать к одному из следующих типов.
  - Рабочие события. Это журналы операций, выполняемых платформой Service Fabric. Некоторые примеры: создание приложений и служб, изменение состояния узлов и сведения об обновлении.
  - [События модели программирования на основе субъектов.](service-fabric-reliable-actors-diagnostics.md)
  - [События модели программирования на основе Reliable Services.](service-fabric-reliable-services-diagnostics.md)
2. **События приложения**. Это события, которые генерируются кодом служб и записываются с помощью вспомогательного класса EventSource, предоставленного в шаблонах Visual Studio. Дополнительные сведения о способах записи журналов из приложения см. [в статье о мониторинге и диагностике служб при настройке локального компьютера](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Развертывание расширения системы диагностики
Первым шагом при сборе журналов является развертывание расширения системы диагностики на каждой виртуальной машине в кластере Service Fabric. Расширение системы диагностики собирает журналы на каждой виртуальной машине и отправляет их в указанную учетную запись хранения. Действия могут немного отличаться в зависимости от следующих факторов: вы используете портал Azure или диспетчер ресурсов Azure, развертывание выполняется в ходе создания кластера или для уже существующего кластера. Рассмотрим действия для каждого сценария.

### Развертывание расширения системы диагностики в ходе создания кластера с помощью портала
Для развертывания расширения системы диагностики на виртуальных машинах в кластере во время его создания используется панель "Параметры диагностики", показанная на рисунке ниже. Для параметра *Журналы поддержки* по умолчанию задано значение **Вкл.**, а для параметра *Диагностика приложений* — **Выкл.**. После создания кластера эти параметры нельзя изменить на портале.

![Настройка системы диагностики Azure на портале для создания кластера](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

Журналы поддержки **необходимы** группе поддержки Azure при рассмотрении запросов на поддержку. Эти журналы собираются в режиме реального времени и сохраняются в учетной записи хранения, созданной в текущей группе ресурсов. Система диагностики приложений настраивает события на уровне приложения, в том числе события [субъекта](service-fabric-reliable-actors-diagnostics.md) и [надежных служб](service-fabric-reliable-services-diagnostics.md), и некоторые события Service Fabric на уровне системы, которые сохраняются в службе хранилища Azure. С помощью средств, таких как [Operational Insights](https://azure.microsoft.com/services/operational-insights/), или собственных средств можно получать события из учетной записи хранения. Сегодня не существует способа фильтрации или очистки событий, которые отправляются в таблицу. Если не реализовать метод удаления событий из таблицы, она продолжит расти. При создании кластера с помощью портала после завершения развертывания мы советуем вам экспортировать шаблон. Чтобы экспортировать шаблон с портала, сделайте следующее:
1. Откройте группу ресурсов.
2. Выберите "Параметры", чтобы отобразить панель "Параметры".
3. Щелкните "Развертывания", чтобы отобразить панель "История развертывания".
4. Выберите определенное развертывание, чтобы отобразить подробные сведения о нем.
5. Щелкните "Экспортировать шаблон", чтобы отобразить панель "Шаблон".
6. Щелкните "Сохранить в файл", чтобы экспортировать ZIP-файл, содержащий шаблон, а также файл параметров и PowerShell.

После экспорта файлов необходимо внести определенные изменения. В файле **parameters.json** удалите элемент **adminPassword**. Таким образом, при запуске скрипта развертывания будет запрашиваться пароль.

### Развертывание расширения системы диагностики в ходе создания кластера с помощью Azure Resource Manager
Чтобы создать кластер с помощью диспетчера ресурсов, добавьте JSON-файл конфигурации системы диагностики в полный шаблон диспетчера ресурсов кластера перед созданием кластера. Мы предоставляем пример шаблона диспетчера ресурсов для кластера из пяти виртуальных машин с конфигурацией системы диагностики (эта конфигурация входит в примеры шаблонов диспетчера ресурсов). Этот шаблон можно найти в коллекции примеров Azure. См. статью [Пример шаблона диспетчера ресурсов — кластер из пяти узлов с системой диагностики](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad). Чтобы просмотреть параметр системы диагностики в шаблоне диспетчера ресурсов, откройте файл **azuredeploy.json** и выполните поиск **IaaSDiagnostics**. Чтобы создать кластер с помощью этого шаблона, нажмите кнопку **Развернуть в Azure**, которая доступна по ссылке выше.

Также можно скачать пример шаблона диспетчера ресурсов, внести в него изменения и создать кластер на основе измененного шаблона с помощью команды `New-AzureResourceGroupDeployment` в окне Azure PowerShell. Ниже приведены параметры, которые необходимо передать в команду. Дополнительные сведения о том, как развернуть группу ресурсов с помощью PowerShell, см. в статье [Развертывание группы ресурсов с использованием шаблона диспетчера ресурсов Azure](../resource-group-template-deploy.md).

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### Развертывание расширения системы диагностики в существующем кластере
Если у вас есть кластер, в котором не развернута система диагностики, ее можно добавить, выполнив следующие действия. Измените шаблон Azure Resource Manager (ARM), который используется для создания существующего кластера, или скачайте шаблон на портале, как описано выше. Измените файл **template.json**, выполнив следующие действия:

Добавьте новый ресурс хранилища в шаблон, внеся изменения в раздел resources.

##### Обновление раздела resources
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

 Затем добавьте этот ресурс в раздел parameters сразу после определений учетной записи хранения между supportLogStorageAccountName и vmNodeType0Name. Замените текст заполнителя *имя учетной записи хранения* именем нужной учетной записи хранения.

##### Обновление раздела parameters
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
Затем в файле *template.json* добавьте в раздел **VirtualMachineProfile** следующий код в массиве extensions. Обязательно добавьте запятую в конце или в начале в зависимости от места вставки.

##### Добавление в массив extensions раздела VirtualMachineProfile
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

Изменив файл **template.json**, как описано, выполните повторную публикацию шаблона ARM. Если шаблон экспортирован, это можно сделать, выполнив файл **deploy.ps1**. После развертывания убедитесь, что значение параметра *ProvisioningState* — *Succeeded*.


## Обновление службы диагностики для сбора и отправки журналов из новых каналов EventSource
Чтобы обновить службу диагностики для сбора журналов из новых каналов EventSource, представляющих новое приложение, которое вы собираетесь развернуть, выполните шаги из [раздела выше](#deploywadarm), в которых описана настройка службы диагностики для существующего кластера. В файле *template.json* вам нужно будет добавить в раздел **EtwEventSourceProviderConfiguration** записи для нового канала EventSources. Это следует сделать до того, как вы обновите конфигурацию с помощью команды PowerShell *New-AzureResourceGroupDeployment*.


## Дальнейшие действия
Ознакомьтесь с диагностическими событиями, которые создаются для [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) и [Reliable Services](service-fabric-reliable-services-diagnostics.md), чтобы лучше понять, на какие события необходимо обращать внимание во время устранения неполадок.

<!---HONumber=AcomDC_0330_2016-->