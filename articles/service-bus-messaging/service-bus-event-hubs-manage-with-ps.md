---
title: "Управление ресурсами служебной шины и концентраторов событий с помощью PowerShell | Документация Майкрософт"
description: "Создание ресурсов служебной шины и концентраторов событий и управление этими ресурсами с помощью PowerShell"
services: service-bus-messaging,event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f6436f6f-2156-41ea-a4ca-08cfaecbb778
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 95068f046c57bd65b5258771a272e70124f3dd12


---
# <a name="use-powershell-to-manage-service-bus-and-event-hubs-resources"></a>Управление ресурсами служебной шины и концентраторов событий с помощью PowerShell
Microsoft Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания служб Azure, а также для управления ими. В этой статье описывается, как с помощью локальной консоли Azure PowerShell можно подготовить сущности служебной шины и управлять ими. В частности, речь пойдет о пространствах имен, очередях и концентраторах событий.

## <a name="prerequisites"></a>Предварительные требования
Для этого потребуются следующие компоненты.

* Подписка Azure. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. на страницах [варианты приобретения][варианты приобретения], [предложения для участников][предложения для участников] или [бесплатная учетная запись][бесплатная учетная запись].
* Компьютер с Azure PowerShell. Инструкции см. в статье [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
* Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## <a name="include-a-reference-to-the-net-assembly-for-service-bus"></a>Добавление ссылки на сборку .NET для служебной шины
Для управления служебной шиной доступно ограниченное число командлетов PowerShell. Для подготовки сущностей, которые недоступны с помощью существующих командлетов, можно использовать клиент .NET для служебной шины прямо в PowerShell, создав ссылку на [пакет NuGet служебной шины].

Сначала убедитесь, что используемый сценарий может найти сборку **Microsoft.ServiceBus.dll** , которая устанавливается вместе с пакетом NuGet. Для гибкости сценарий выполняет такие действия:

1. Определяет путь, по которому он был вызван.
2. Проходит по пути и находит папку с именем `packages`. Эта папка создается во время установки пакетов NuGet.
3. Рекурсивно ищет в папке `packages` сборку с именем **Microsoft.ServiceBus.dll**.
4. Создает ссылку на сборку, чтобы типы стали доступны для дальнейшего использования.

В сценарии PowerShell эти действия выполняются следующим образом:

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## <a name="provision-a-service-bus-namespace"></a>Подготовка пространства имен Service Bus
При работе с пространствами имен служебной шины вместо пакета SDK для .NET можно использовать два командлета: [Get-AzureSBNamespace][Get-AzureSBNamespace] и [New-AzureSBNamespace][New-AzureSBNamespace].

В этом примере мы создадим несколько локальных переменных в сценарии, в частности `$Namespace` и `$Location`.

* `$Namespace` — имя пространства имен служебной шины, которое мы будем использовать.
* `$Location` — центр обработки данных, в котором мы подготовим пространство имен к работе.
* `$CurrentNamespace` — место хранения полученного (или созданного) исходного пространства имен.

В фактическом сценарии переменные `$Namespace` и `$Location` могут передаваться как параметры.

Эта часть сценария выполняет следующее:

1. Пытается получить пространство имен служебной шины с заданным именем.
2. Если пространство имен найдено, появляется сообщение о том, что именно нашел сценарий.
3. Если пространство имен не найдено, сценарий создает его и возвращает созданное пространство.
   
    ``` powershell
   
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
   
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```
   Чтобы подготовить другие сущности служебной шины к работе, создайте экземпляр объекта `NamespaceManager` из пакета SDK. Получить правило авторизации для указания строки подключения можно с помощью командлета [Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule]. В этом примере ссылка на экземпляр `NamespaceManager` будет сохранена в переменной `$NamespaceManager`. Позже в сценарии будет использоваться переменная `$NamespaceManager` для подготовки других сущностей.
   
    ``` powershell
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    # Create the NamespaceManager object to create the Event Hub
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
    ```

## <a name="provisioning-other-service-bus-entities"></a>Подготовка других сущностей Service Bus
Чтобы подготовить к работе другие сущности, такие как очереди, разделы и концентраторы событий, используйте [API .NET для служебной шины][API .NET для служебной шины]. В конце этой статьи приведены ссылки на более подробные примеры, включая примеры с другими сущностями.

### <a name="create-an-event-hub"></a>Создание концентратора событий
В этой части сценария мы создадим четыре дополнительных локальных переменных, которые будут использоваться для создания экземпляра объекта `EventHubDescription`. Сценарий выполняет следующее:

1. С помощью объекта `NamespaceManager` проверяет, существует ли концентратор событий, определенный в переменной `$Path`.
2. Если он не существует, создает и передает `EventHubDescription` в класс `NamespaceManager` метода `CreateEventHubIfNotExists`.
3. Когда концентратор событий становится доступен, сценарий создает группу потребителей с помощью `ConsumerGroupDescription` и `NamespaceManager`.
   
    ``` powershell
   
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
   
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

### <a name="create-a-queue"></a>Создание очереди
Чтобы создать очередь или раздел, проверьте пространство имен, как описано в предыдущем разделе.

    if ($NamespaceManager.QueueExists($Path))
    {
        Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
        $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
        $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
        $QueueDescription.EnableExpress = $EnableExpress
        $QueueDescription.EnablePartitioning = $EnablePartitioning
        $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
        $QueueDescription.ForwardTo = $ForwardTo
        $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        if ($LockDuration -gt 0)
        {
            $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
        }
        $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
        $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        $QueueDescription.RequiresSession = $RequiresSession
        if ($EnablePartitioning)
        {
            $QueueDescription.SupportOrdering = $False
        }
        else
        {
            $QueueDescription.SupportOrdering = $SupportOrdering
        }
        $QueueDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateQueue($QueueDescription);
    }

### <a name="create-a-topic"></a>Создание раздела
    if ($NamespaceManager.TopicExists($Path))
    {
        Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
        $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
        $TopicDescription.EnableExpress = $EnableExpress
        $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
        $TopicDescription.EnablePartitioning = $EnablePartitioning
        $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        if ($EnablePartitioning)
        {
            $TopicDescription.SupportOrdering = $False
        }
        else
        {
            $TopicDescription.SupportOrdering = $SupportOrdering
        }
        $TopicDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateTopic($TopicDescription);
    }

## <a name="next-steps"></a>Дальнейшие действия
В этой статье описан базовый рабочий процесс подготовки сущностей служебной шины с помощью PowerShell. Количество командлетов PowerShell для управления сущностями обмена сообщениями служебной шины ограничено, но ссылаясь на сборку Microsoft.ServiceBus.dll в сценарии PowerShell, можно выполнить практически любую операцию, которую также можно выполнить с помощью клиентских библиотек .NET.

Более подробные примеры приведены в следующих записях блога:

* [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Как создать пространство имен и концентратор событий служебной шины с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Некоторые готовые сценарии доступны для скачивания:

* [Сценарии PowerShell для Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[варианты приобретения]: http://azure.microsoft.com/pricing/purchase-options/
[предложения для участников]: http://azure.microsoft.com/pricing/member-offers/
[бесплатная учетная запись]: http://azure.microsoft.com/pricing/free-trial/
[пакет NuGet служебной шины]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET для служебной шины]: https://msdn.microsoft.com/en-us/library/azure/mt419900.aspx
[Установка и настройка Azure PowerShell]: ../powershell-install-configure.md



<!--HONumber=Nov16_HO3-->


