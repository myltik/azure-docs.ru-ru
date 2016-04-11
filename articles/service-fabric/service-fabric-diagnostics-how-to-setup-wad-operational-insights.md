<properties
   pageTitle="Сбор журналов с помощью системы диагностики Azure и оперативной аналитики Azure | Microsoft Azure"
   description="В этой статье описывается, как настроить систему диагностики Azure и оперативную аналитику Azure для сбора журналов из кластера Service Fabric, запущенного в Azure."
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


# Сбор журналов из кластера Service Fabric с помощью системы диагностики Azure и оперативной аналитики

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Если все журналы хранятся централизованно, это упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере. Для отправки и сбора журналов рекомендуется использовать расширение системы диагностики Azure, которое отправляет журналы в службу хранилища Azure.

[Оперативная аналитика](https://azure.microsoft.com/services/operational-insights/) Azure (входит в пакет Microsoft Operations Management Suite) является решением типа SaaS (программное обеспечение как услуга), которое позволяет легко анализировать журналы и выполнять в них поиск. Ниже описано, как настроить расширение системы диагностики Azure на виртуальных машинах в кластере для отправки журналов в центральное хранилище. Затем вы сможете настроить оперативную аналитику для извлечения журналов с их последующим просмотром на портале оперативной аналитики.

Оперативная аналитика определяет источники журналов разных типов, отправленных из кластера Service Fabric, по именам таблиц хранилища, в которых они хранятся. Поэтому расширение системы диагностики Azure нужно настроить для отправки журналов в таблицы хранилища с именами, которые будет искать оперативная аналитика. На примерах настроек конфигурации в этом документе вы увидите, какими должны быть имена таблиц хранилища.


## Предварительные требования
Эти инструменты будут использоваться для некоторых операций в данном документе:

* [Azure PowerShell](../powershell-install-configure.md)
* [Operational Insights;](https://azure.microsoft.com/services/operational-insights/)


## Настройка оперативной аналитики для просмотра журналов кластера и поиска в них
В статье, посвященной [сбору журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md), содержится пошаговое руководство по включению отправки журналов в учетную запись хранения Azure. Когда система диагностики будет установлена в кластере и начнет отправлять журналы в учетную запись хранения, нужно настроить оперативную аналитику. Это позволит вам просматривать все журналы кластера, выполнять в них поиск и отправлять в них запросы через портал оперативной аналитики.

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

Настроив для рабочей области оперативной аналитики чтение данных из таблиц в учетной записи хранения Azure, войдите на портал и перейдите на вкладку **Служба хранилища** ресурса оперативной аналитики. Должно отобразиться примерно следующее: ![Настройка хранилища оперативной аналитики на портале Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Просмотр журналов и поиск в них с помощью оперативной аналитики
После того как вы настроите рабочую область оперативной аналитики для чтения журналов из указанной учетной записи хранения, журналы отобразятся в пользовательском интерфейсе оперативной аналитики только через 10 минут. Чтобы генерировались новые журналы, рекомендуем развернуть приложение Service Fabric в кластере: оно будет генерировать рабочие события из платформы Service Fabric.

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

<!---HONumber=AcomDC_0330_2016-->