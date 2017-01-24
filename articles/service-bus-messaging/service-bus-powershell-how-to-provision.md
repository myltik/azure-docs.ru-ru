---
title: "Управление служебной шиной с помощью PowerShell | Документация Майкрософт"
description: "Управление служебной шиной с помощью сценариев PowerShell"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: aff8e2ce-bc8b-489f-aca9-a18782be0375
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: 5b6c396d89816bb2804cc63beb860f4628333cb7


---
# <a name="manage-service-bus-with-powershell"></a>Управление Service Bus с помощью PowerShell
## <a name="overview"></a>Обзор
Microsoft Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье описывается, как с помощью локальной консоли Azure PowerShell можно подготовить сущности служебной шины и управлять ими. В частности, речь пойдет о пространствах имен, очередях и концентраторах событий.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этой статьей необходимо следующее:

* Подписка Azure. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. на страницах [Как приобрести Azure][Purchase Options], [Предложения для участников][Member Offers] или [Создайте бесплатную учетную запись Azure уже сегодня][Free Trial].
* Компьютер с Azure PowerShell. Инструкции по установке и настройке Azure PowerShell см. в статье [Установка и настройка служб Azure PowerShell][Install and configure Azure PowerShell].
* Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Добавление ссылки на сборку .NET для Service Bus
Для управления служебной шиной доступно ограниченное число командлетов PowerShell. Для подготовки сущностей, которые недоступны через имеющиеся командлеты, можно использовать клиент .NET для служебной шины в [пакете NuGet служебной шины][Service Bus NuGet package].

Сначала убедитесь, что используемый сценарий может найти сборку **Microsoft.ServiceBus.dll** , которая устанавливается вместе с пакетом NuGet. Для гибкости сценарий выполняет такие действия:

1. Определяет путь, по которому он был вызван.
2. Проходит по пути и находит папку с именем `packages`. Эта папка создается во время установки пакетов NuGet.
3. Рекурсивно ищет в папке `packages` сборку с именем **Microsoft.ServiceBus.dll**.
4. Создает ссылку на сборку, чтобы типы стали доступны для дальнейшего использования.

В сценарии PowerShell эти действия выполняются следующим образом:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Подготовка пространства имен Service Bus
Два командлета PowerShell поддерживают операции пространства имен служебной шины. Вместо интерфейсов API для пакета SDK для .NET можно использовать [Get-AzureSBNamespace][Get-AzureSBNamespace] и [New-AzureSBNamespace][New-AzureSBNamespace].

В этом примере мы создадим несколько локальных переменных в сценарии, в частности `$Namespace` и `$Location`.

* `$Namespace` — имя пространства имен служебной шины, которое мы будем использовать.
* `$Location` — центр обработки данных, в котором сценарий подготавливает пространство имен к работе.
* `$CurrentNamespace` — место хранения полученного (или созданного) исходного пространства имен в сценарии.

В фактическом сценарии переменные `$Namespace` и `$Location` могут передаваться как параметры.

Эта часть скрипта выполняет следующее:

1. Пытается получить пространство имен служебной шины с заданным именем.
2. Если пространство имен найдено, появляется сообщение о том, что именно нашел сценарий.
3. Если пространство имен не найдено, сценарий создает его и возвращает созданное пространство.
   
    ```
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
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Чтобы подготовить другие сущности служебной шины к работе, создайте экземпляр класса [NamespaceManager][NamespaceManager] из пакета SDK.
Получить правило авторизации для указания строки подключения можно с помощью командлета [Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule]. Ссылка на экземпляр `NamespaceManager` будет сохранена в переменной `$NamespaceManager`. В дальнейшем мы используем переменную `$NamespaceManager` в сценарии для подготовки к работе других сущностей.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Подготовка других сущностей Service Bus
Чтобы подготовить к работе другие сущности, такие как очереди, разделы и концентраторы событий, используйте [API .NET для служебной шины][.NET API for Service Bus]. В этой статье рассматриваются только концентраторы событий, но действия для других сущностей выполняются таким же образом. Кроме того, в конце статьи приведены более подробные примеры, включая примеры с другими сущностями.

В этой части сценария мы создадим четыре дополнительных локальных переменных, которые будут использоваться для создания экземпляра объекта `EventHubDescription`. Скрипт выполняет следующие задачи:

1. С помощью объекта `NamespaceManager` проверяет, существует ли концентратор событий, определенный в переменной `$Path`.
2. Если он не существует, создает и передает `EventHubDescription` в класс `NamespaceManager` метода `CreateEventHubIfNotExists`.
3. Когда концентратор событий становится доступен, сценарий создает группу потребителей с помощью `ConsumerGroupDescription` и `NamespaceManager`.
   
    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check to see if the Event Hub already exists
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

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Перенос пространства имен в другую подписку Azure
Следующая последовательность команд перемещает пространство имен из одной подписки Azure в другую. Для выполнения этой операции пространство имен уже должно быть активным, а пользователь, выполняющий команды PowerShell, должен обладать правами администратора в исходной и целевой подписках.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Дальнейшие действия
В этой статье описана базовая схема подготовки к работе сущности служебной шины с помощью PowerShell. Все действия, которые можно выполнить с помощью клиентских библиотек .NET, также доступны для сценария PowerShell.

Более подробные примеры приведены в следующих записях блога:

* [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Как создать пространство имен и концентратор событий служебной шины с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Некоторые готовые сценарии доступны для скачивания:

* [Сценарии PowerShell для Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Purchase Options]: http://azure.microsoft.com/pricing/purchase-options/
[Member Offers]: http://azure.microsoft.com/pricing/member-offers/
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[.NET API for Service Bus]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx



<!--HONumber=Dec16_HO1-->


