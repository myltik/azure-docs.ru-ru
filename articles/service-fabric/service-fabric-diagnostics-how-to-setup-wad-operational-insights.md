<properties
   pageTitle="Сбор журналов с помощью системы диагностики Azure и оперативной аналитики Azure | Microsoft Azure"
   description="В этой статье описывается, как настроить систему диагностики Azure и оперативную аналитику Azure для сбора журналов из кластера Service Fabric, запущенного в Azure."
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


# Сбор журналов из кластера Service Fabric с помощью системы диагностики Azure и оперативной аналитики

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Если все журналы хранятся централизованно, это упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере. Для отправки и сбора журналов рекомендуется использовать расширение системы диагностики Azure, которое отправляет журналы в службу хранилища Azure.

Оперативная аналитика Azure (входит в пакет Microsoft Operations Management Suite) является решением типа SaaS (программное обеспечение как услуга), которое позволяет легко анализировать журналы и выполнять в них поиск. Ниже описано, как настроить расширение системы диагностики Azure на виртуальных машинах в кластере для отправки журналов в центральное хранилище. Затем вы сможете настроить оперативную аналитику для извлечения журналов с их последующим просмотром на портале оперативной аналитики.

Оперативная аналитика определяет источники журналов разных типов, отправленных из кластера Service Fabric, по именам таблиц хранилища, в которых они хранятся. Поэтому расширение системы диагностики Azure нужно настроить для отправки журналов в таблицы хранилища с именами, которые будет искать оперативная аналитика. На примерах настроек конфигурации в этом документе вы увидите, какими должны быть имена таблиц хранилища.

## Материалы для чтения
* [Система диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (статья посвящена облачным службам Azure, но содержит нужную информацию и примеры).
* [Operational Insights;](https://azure.microsoft.com/services/operational-insights/)
* [Диспетчер ресурсов Azure](https://azure.microsoft.com/resource-group-overview/)

## Предварительные требования
Для выполнения некоторых операций в этом документе будут использоваться следующие средства: [Azure PowerShell](https://azure.microsoft.com/powershell-install-configure/), [клиент диспетчера ресурсов Azure](https://github.com/projectkudu/ARMClient).

## Различные источники журналов, которые вы можете собирать
1. **Журналы Service Fabric**. Генерируются платформой в стандартные каналы трассировки событий Windows (ETW) и EventSource. Журналы могут принадлежать к одному из следующих типов.
  - Рабочие события. Это журналы операций, выполняемых платформой Service Fabric. Некоторые примеры: создание приложений и служб, изменение состояния узлов и сведения об обновлении.
  - [События модели программирования на основе субъектов.](service-fabric-reliable-actors-diagnostics.md)
  - [События модели программирования на основе Reliable Services.](service-fabric-reliable-services-diagnostics.md)
2. **События приложения**. Это события, которые генерируются кодом служб и записываются с помощью вспомогательного класса EventSource, предоставленного в шаблонах Visual Studio. Дополнительные сведения о способах записи журналов из приложения см. [в статье о мониторинге и диагностике служб при настройке локального компьютера](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Развертывание расширения системы диагностики в кластере Service Fabric для сбора и отправки журналов
Первым шагом при сборе журналов является развертывание расширения системы диагностики на каждой виртуальной машине в кластере Service Fabric. Расширение системы диагностики собирает журналы на каждой виртуальной машине и отправляет их в указанную учетную запись хранения. Действия могут немного отличаться в зависимости от следующих факторов: вы используете портал Azure или диспетчер ресурсов Azure, развертывание выполняется в ходе создания кластера или для уже существующего кластера. Рассмотрим действия для каждого сценария.

### Развертывание расширения системы диагностики в ходе создания кластера с помощью портала
Для развертывания расширения системы диагностики на виртуальных машинах в кластере во время его создания используется параметр системы диагностики, показанный на рисунке ниже. По умолчанию этот параметр **включен**. ![Настройка системы диагностики Azure на портале для создания кластера](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Развертывание расширения системы диагностики в ходе создания кластера с помощью диспетчера ресурсов Azure
Чтобы создать кластер с помощью диспетчера ресурсов, добавьте JSON-файл конфигурации системы диагностики в полный шаблон диспетчера ресурсов кластера перед созданием кластера. Мы предоставляем пример шаблона диспетчера ресурсов для кластера из пяти виртуальных машин с конфигурацией системы диагностики (эта конфигурация входит в примеры шаблонов диспетчера ресурсов). Этот шаблон можно найти в коллекции примеров Azure. См. статью [Пример шаблона диспетчера ресурсов — кластер из пяти узлов с системой диагностики](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Чтобы просмотреть параметр системы диагностики в шаблоне диспетчера ресурсов, найдите запись **WadCfg.** Чтобы создать кластер с помощью этого шаблона, нажмите кнопку **Развернуть в Azure**, которая доступна по ссылке выше. Также можно скачать пример шаблона диспетчера ресурсов, внести в него изменения и создать кластер на основе измененного шаблона с помощью команды `New-AzureResourceGroupDeployment` в окне Azure PowerShell. Ниже приведены параметры, которые необходимо передать в команду.

Кроме того, перед вызовом этой команды развертывания может потребоваться выполнить некоторые настройки: добавить учетную запись Azure (`Add-AzureAccount`), выбрать подписку (`Select-AzureSubscription`), переключиться в режим диспетчера ресурсов (`Switch-AzureMode AzureResourceManager`) и создать группу ресурсов, если она еще не создана (`New-AzureResourceGroup`).

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Развертывание расширения системы диагностики в существующем кластере
Если у вас есть кластер, в котором не развернута система диагностики, ее можно добавить, выполнив следующие действия. Создайте два файла — WadConfigUpdate.json и WadConfigUpdateParams.json — с помощью приведенного ниже кода JSON.

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
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
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
Замените vmNamePrefix префиксом, который вы выбрали для имен виртуальных машин при создании кластера. Затем замените vmStorageAccountName именем учетной записи хранения, в которую будут отправляться журналы с виртуальных машин.

```json

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
```

После создания JSO-файлов, как указано выше, внесите в них изменения с учетом особенностей вашей среды. Затем вызовите следующую команду, передав имя группы ресурсов для кластера Service Fabric. После успешного выполнения этой команды система диагностики будет развернута на всех виртуальных машинах и начнет отправлять журналы из кластера в таблицы в указанной учетной записи хранения Azure.

Кроме того, перед вызовом этой команды развертывания вам может понадобиться выполнить некоторые настройки: добавить учетную запись Azure (`Add-AzureAccount`), выбрать подписку (`Select-AzureSubscription`) и переключиться в режим диспетчера ресурсов (`Switch-AzureMode AzureResourceManager`).

```ps

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Настройка оперативной аналитики для просмотра журналов кластера и поиска в них
Когда система диагностики будет установлена в кластере и начнет отправлять журналы в учетную запись хранения, нужно настроить оперативную аналитику. Это позволит вам просматривать все журналы кластера, выполнять в них поиск и отправлять в них запросы через портал оперативной аналитики.

### Создание рабочей области оперативной аналитики
Шаги по созданию рабочей области оперативной аналитики см. в указанной ниже статье. Обратите внимание, что в ней описаны два разных способа создания рабочей области. Выберите вариант с использованием портала Azure и подписки. В следующих разделах этого документа вам потребуется имя рабочей области оперативной аналитики. Создавайте рабочую область оперативной аналитики с помощью той же подписки, которую вы использовали для создания всех ресурсов кластера, в том числе учетных записей хранения.

[Процесс внедрения оперативной аналитики](https://technet.microsoft.com/library/mt484118.aspx)

### Настройка рабочей области оперативной аналитики для отображения журналов кластера
Создав рабочую область оперативной аналитики по приведенным выше инструкциям, можно приступать к настройке рабочей области для извлечения журналов из таблиц в службе хранилища Azure, в которые расширение системы диагностики отправляет журналы из кластера. Сейчас настройка через портал оперативной аналитики не поддерживается. Ее можно выполнить только с помощью команд PowerShell. Выполните следующий сценарий PowerShell:

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

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

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

Настроив рабочую область Operational Insights на чтение данных из таблиц в учетной записи хранилища Azure, войдите на портал и перейдите на вкладку **Служба хранилища** ресурса Operational Insights. Должно отобразиться примерно следующее: ![Настройка хранилища оперативной аналитики на портале Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Просмотр журналов и поиск в них с помощью оперативной аналитики
После того как вы настроите рабочую область оперативной аналитики для чтения журналов из указанной учетной записи хранения, журналы отобразятся в пользовательском интерфейсе оперативной аналитики только через 10 минут. Чтобы генерировались новые журналы, рекомендуем развернуть приложение Service Fabric в кластере: оно будет генерировать рабочие события из платформы Service Fabric.

1. Чтобы просмотреть журналы, выберите **Поиск по журналам** на главной странице портала оперативной аналитики. ![Поиск по журналам в оперативной аналитике](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. На странице поиска по журналам введите запрос **Type=ServiceFabricOperationalEvent**, чтобы просмотреть операционные журналы из кластера, как показано ниже. Чтобы просмотреть все журналы модели программирования на основе субъектов, используйте запрос **Type=ServiceFabricReliableActorEvent**. Чтобы просмотреть все журналы в модели программирования на основе Reliable Services, введите **Type=ServiceFabricReliableServiceEvent**. ![Просмотр журнала в оперативной аналитике и отправка запроса в него](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Примеры запросов для устранения проблем
Ниже приведены примеры нескольких сценариев и запросов, которые можно использовать для устранения неполадок.

1. **Узнайте, вызывала ли платформа Service Fabric команду RunAsync для конкретной службы.** Вам может понадобиться эта информация, чтобы узнать, был ли сбой во время запуска службы. Чтобы получить эту информацию, выполните поиск с помощью запроса, похожего на приведенный ниже, заменив имя службы и приложения именем развернутых службы и приложения, и проверьте, возвращаются ли результаты.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **Если вы запустили службу с отслеживанием состояния и хотите знать, выдавала ли она исключения, отмеченные Service Fabric как сбои**, найдите эти события с помощью следующего запроса:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **Чтобы найти события, соответствующие исключениям, выданным методами субъектов во всех развернутых приложениях и службах**, используйте следующий запрос.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Обновление службы диагностики для сбора и отправки журналов из новых каналов EventSource
Чтобы обновить службу диагностики для сбора журналов из новых каналов EventSource, представляющих новое приложение, которое вы собираетесь развернуть, выполните шаги из [раздела выше](#deploywadarm), в которых описана настройка службы диагностики для существующего кластера.

В файле WadConfigUpdate.json вам нужно будет добавить в раздел EtwEventSourceProviderConfiguration записи для нового канала EventSources. Это нужно сделать до того, как вы обновите конфигурацию с помощью команды диспетчера ресурсов. Для отправки будет использоваться та же таблица (ETWEventTable), так как она настроена в оперативной аналитике для чтения событий ETW из приложения.


## Дальнейшие действия
Ознакомьтесь с диагностическими событиями, которые создаются для [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) и [Reliable Services](service-fabric-reliable-services-diagnostics.md), чтобы лучше понять, на какие события необходимо обращать внимание во время устранения неполадок.

<!---HONumber=AcomDC_0211_2016-->