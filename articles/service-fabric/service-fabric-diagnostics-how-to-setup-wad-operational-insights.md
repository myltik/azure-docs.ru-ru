<properties
   pageTitle="Сбор журналов с помощью WAD и оперативной аналитики | Microsoft Azure"
   description="В этой статье описывается, как можно настроить систему диагностики Microsoft Azure и оперативную аналитику для сбора журналов из кластера Service Fabric, запущенного в Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# Сбор журналов из кластера Service Fabric в Azure с помощью системы диагностики Microsoft Azure (WAD) и оперативная аналитика

Во время работы кластера Service Fabric в Azure вам может понадобиться собирать журналы из всех узлов в центральном расположении. Если все журналы находятся в одном расположении, это упрощает анализ и устранение неполадок, которые могут возникнуть в кластере или приложениях и службах, работающих в этом кластере. Для передачи и сбора журналов рекомендуется использовать расширение WAD (система диагностики Microsoft Azure), которое отправляет журналы в хранилище таблиц Azure. Оперативная аналитика (часть пакета Microsoft Operations Management Suite) является решением SaaS, которое позволяет легко анализировать журналы и выполнять в них поиск. Следующие шаги описывают, как настроить WAD на входящих в кластер виртуальных машинах для отправки журналов в центральное хранилище. После чего вы сможете настроить оперативную аналитику для извлечения журналов с их последующим просмотром на портале оперативной аналитики. Оперативная аналитика определяет источники журналов разных типов, отправленных из кластера Service Fabric, по именам таблиц хранилища Azure, в которых они хранятся. Поэтому WAD нужно настроить для передачи журналов в таблицы хранилища Azure с именами, которые ищет оперативная аналитика. Примеры настроек конфигурации в этом документе покажут вам, какими должны быть имена таблиц хранилища.

## Материалы для чтения
* [Система диагностики Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (относится к облачным службам, но содержит нужную информацию и хорошие примеры)
* [Operational Insights;](https://azure.microsoft.com/services/operational-insights/)
* [Диспетчер ресурсов Azure](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## Предварительные требования
Эти средства будут использоваться для выполнения некоторых операций в этом документе: * [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [клиент ARM](https://github.com/projectkudu/ARMClient).

## Различные источники журналов, которые вы можете собирать
1. Журналы Service Fabric — генерируются платформой в стандартные каналы трассировки событий Windows и EventSource. Это могут быть журналы следующих типов.
  - Рабочие события — это журналы операций, выполняемых платформой Service Fabric. Примеры включают создание приложения и службы, изменения состояния узла и сведения об обновлении.
  - [События модели программирования на основе субъектов](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/).
  - [События модели программирования на основе Reliable Services](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/).
2. События приложения — события, генерируемые кодом служб и записанные с помощью вспомогательного класса EventSource, предоставленного в шаблонах Visual Studio. Дополнительные сведения о способах записи журналов из приложения см. в этой [статье](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/).


## Развертывание WAD (системы диагностики Microsoft Azure) в кластере Service Fabric для сбора и передачи журналов
Первым действием по сбору журналов будет развертывание расширения WAD на каждой виртуальной машине в кластере Service Fabric. WAD будет собирать журналы на каждой виртуальной машине и передавать их в указанную учетную запись хранения. Действия могут немного отличаться в зависимости от следующих факторов: вы используете портал или ARM, развертывание выполняется в ходе создания кластера или для уже существующего кластера. Рассмотрим действия для каждого сценария.

### Развертывание WAD в ходе создания кластера с помощью портала
Для развертывания WAD на виртуальных машинах в кластере в ходе его создания используется параметр системы диагностики, показанный на рисунке ниже. По умолчанию этот параметр включен. ![Параметр WAD на портале для создания кластера](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Развертывание WAD в ходе создания кластера с помощью ARM
Чтобы создать кластер с помощью ARM, добавьте конфигурацию JSON для WAD в полный шаблон ARM кластера перед созданием кластера. Мы предоставляем пример шаблона ARM для кластера из пяти виртуальных машин с конфигурацией WAD (добавлено как часть наших примеров шаблона ARM). Вы можете найти шаблон в его расположении в коллекции примеров Azure: [Кластер из пяти узлов с примером шаблона ARM для WAD](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Чтобы просмотреть параметр WAD в шаблоне ARM, выполните поиск по слову WadCfg. Чтобы создать кластер с помощью этого шаблона, нажмите кнопку «Развернуть для Azure», которая доступна по ссылке выше. Или вы можете загрузить образец ARM, внести в него изменения и создать кластер с измененным шаблоном с помощью команды `New-AzureResourceGroupDeployment` в окне Azure Powershell. Ниже приведены параметры, которые необходимо передать в команду. Кроме того, перед вызовом этой команды развертывания может потребоваться выполнить некоторые настройки, в том числе добавить учетную запись Azure (`Add-AzureAccount`), выбрать подписку (`Select-AzureSubscription`), переключиться в режим ARM (`Switch-AzureMode AzureResourceManager`) и создать группу ресурсов, если вы еще не создали ее (`New-AzureResourceGroup`).

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Развертывание WAD в существующем кластере
Если у вас есть кластер, в котором не развернута система WAD, вы можете добавить WAD, выполнив описанные ниже действия. Создайте два файла WadConfigUpdate.json и WadConfigUpdateParams.json с помощью JSON ниже.

##### WadConfigUpdate.json
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
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
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
Замените vmNamePrefix префиксом, который вы выбрали для имен виртуальных машин во время создания кластера, и измените vmStorageAccountName для учетной записи хранения, в которую будут передаваться журналы из виртуальных машин. ```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
``` После создания файлов json, следуя инструкциям выше, измените их с учетом особенностей вашей среды и вызовите эту команду. Для этого передайте имя группы ресурсов для кластера Service Fabric. После успешного выполнения этой команды система WAD будет развернута на всех виртуальных машинах и начнет отправлять журналы из кластера в таблицы в указанной учетной записи хранения Azure. Кроме того, перед вызовом этой команды развертывания может потребоваться выполнить некоторые настройки, в том числе добавить учетную запись Azure (`Add-AzureAccount`), выбрать подписку (`Select-AzureSubscription`) и переключиться в режим ARM (`Switch-AzureMode AzureResourceManager`). ```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Настройка оперативной аналитики для просмотра журналов кластера и поиска в них
Теперь, после того как система WAD установлена в кластере и отправляет журналы в учетную запись хранилища, нужно настроить оперативную аналитику. Вы сможете просматривать все журналы кластера, выполнять в них поиск и отправлять в них запросы через пользовательский интерфейс портала оперативной аналитики.

### Создание рабочей области оперативной аналитики
Действия по созданию рабочей области оперативной аналитики см. в статье ниже. Обратите внимание, что статья описывает два способа создания рабочей области. Выберите подход с использованием портала Azure и подписки. В следующих разделах этого документа вам потребуется имя рабочей области оперативной аналитики. Создавайте рабочую область оперативной аналитики с помощью той же подписки, которую вы использовали для создания всех ресурсов кластера, в том числе учетных записей хранилища.

[Процесс внедрения оперативной аналитики](https://technet.microsoft.com/library/mt484118.aspx)

### Настройка рабочей области оперативной аналитики для отображения журнала кластера
После создания рабочей области оперативной аналитики, как описано выше, вы можете перейти к настройке рабочей области для извлечения журналов из таблиц Azure, в которые WAD отправляет журналы из кластера. В настоящее время портал оперативной аналитики не поддерживает эту конфигурацию, поэтому ее можно реализовать только с помощью команд Powershell. Запустите этот сценарий Powershell. ```powershell <# Этот сценарий настраивает рабочую область Operations Management Suite (она же — рабочая область оперативной аналитики) для чтения системы диагностики Microsoft Azure из учетной записи хранения Azure.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
#>
Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager.

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # HTTP Not Found is returned if the storage insight doesn't exist }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` Once you have configured the Operational Insights workspace to read from the Azure Tables in your storage account, you should log into the Azure Portal, and look up the Storage tab for the Operational Insights resource. Должно отобразиться примерно следующее: ![Настройка хранилища оперативной аналитики на портале Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Просмотр журналов в оперативной аналитике и выполнение поиска в них
После настройки рабочей области оперативной аналитики для чтения журналов из указанной учетной записи хранения журналы отобразятся в пользовательском интерфейсе оперативной аналитики только через 10 минут. Чтобы генерировались новые журналы, разверните приложение Service Fabric в кластере: оно будет генерировать рабочие события с платформы Service Fabric.

1. Чтобы просмотреть журналы, выберите LogSearch на главной странице портала оперативной аналитики. ![Поиск по журналу в оперативной аналитике](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. На странице поиска журнала используйте запрос Type=ServiceFabricOperationalEvent, чтобы просмотреть рабочие журналы из кластера, как показано ниже. Для просмотра всех журналов модели программирования на основе субъектов используйте запрос Type=ServiceFabricReliableActorEvent. Чтобы просмотреть все журналы модели программирования на основе Reliable Services, используйте запрос Type=ServiceFabricReliableServiceEven. ![Просмотр журнала в оперативной аналитике и отправка запроса в него](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Примеры запросов для устранения проблем
Ниже приведены примеры нескольких сценариев и запросов, которые можно использовать для устранения неполадок.

1. Узнайте, вызывала ли платформа Service Fabric команду RunAsync для конкретной службы. Вам может понадобиться эта информация, чтобы узнать, был ли сбой во время запуска службы. Чтобы получить эту информацию, выполните поиск с помощью запроса, похожего на запрос ниже, заменив имя службы и приложения именем развернутых службы и приложения, и проверьте, возвращаются ли результаты.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. Если вы запустили службу с отслеживанием состояния и хотите знать, выдавала ли она исключения, отмеченные Service Fabric как сбои, найдите эти события с помощью похожего запроса.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. Чтобы найти события, соответствующие исключениям, выданным методами субъектов во всех развернутых приложениях и службах, используйте запрос, приведенный ниже.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Обновление WAD для сбора и передачи журналов из новых каналов EventSource
Чтобы обновить WAD для сбора журналов из новых каналов EventSource, представляющих новое приложение, которое вы собираетесь развертывать, выполните действия из [раздела выше](#deploywadarm), которые описывают настройку WAD для существующего кластера. Вам нужно будет обновить раздел EtwEventSourceProviderConfiguration в файле WadConfigUpdate.json, чтобы добавить записи для нового канала EventSources до обновления конфигурации с помощью команды ARM. Для передачи будет использоваться та же таблица (ETWEventTable), так как эта таблица настроена в оперативной аналитике для чтения событий из приложения ETW.


## Дальнейшие действия
Ознакомьтесь с событиями диагностики, выпущенными для [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) и [Reliable Services](service-fabric-reliable-services-diagnostics.md), чтобы лучше понять, на какие события необходимо обратить внимание во время устранения неполадок.

<!---HONumber=Nov15_HO4-->