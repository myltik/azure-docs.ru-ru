<properties
	pageTitle="Управление служебной шиной с помощью PowerShell | Microsoft Azure"
	description="Управление Service Bus с помощью сценариев PowerShell вместо .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sethm"/>

# Управление Service Bus с помощью PowerShell

## Обзор

Microsoft Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье описывается, как с помощью локальной консоли Azure PowerShell можно подготовить сущности служебной шины и управлять ими. В частности, речь пойдет о пространствах имен, очередях и концентраторах событий.

## Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][], [Предложения для участников][] или [Бесплатное пробное использование][].

- Компьютер с Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][].

- Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## Добавление ссылки на сборку .NET для Service Bus

Для управления служебной шиной доступно ограниченное число командлетов PowerShell. Для подготовки сущностей, которые недоступны через существующие командлеты, можно использовать клиент .NET для служебной шины в [пакете NuGet служебной шины].

Сначала убедитесь, что используемый сценарий может найти сборку **Microsoft.ServiceBus.dll**, которая устанавливается вместе с пакетом NuGet. Для гибкости сценарий выполняет такие действия:

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
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## Подготовка пространства имен Service Bus

Два командлета PowerShell поддерживают операции пространства имен служебной шины. Вместо интерфейсов API для пакета SDK .NET можно использовать [Get-AzureSBNamespace][] и [New-AzureSBNamespace][].

В этом примере мы создадим несколько локальных переменных в сценарии, в частности `$Namespace` и `$Location`.

- `$Namespace` — имя пространства имен служебной шины, которое мы будем использовать.
- `$Location` — центр обработки данных, в котором сценарий подготавливает пространство имен к работе.
- `$CurrentNamespace` — место хранения полученного (или созданного) исходного пространства имен в сценарии.

В фактическом сценарии переменные `$Namespace` и `$Location` могут передаваться как параметры.

Эта часть сценария выполняет следующее:

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

Чтобы подготовить другие сущности служебной шины к работе, создайте экземпляр класса [NamespaceManager][] из пакета SDK. Получить правило авторизации для указания строки подключения можно с помощью командлета [Get-AzureSBAuthorizationRule][]. Ссылка на экземпляр `NamespaceManager` будет сохранена в переменной `$NamespaceManager`. В дальнейшем мы используем переменную `$NamespaceManager` в сценарии для подготовки к работе других сущностей.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Подготовка других сущностей Service Bus

Чтобы подготовить к работе другие сущности, такие как очереди, разделы и концентраторы событий, используйте [API .NET для служебной шины][]. В этой статье рассматриваются только концентраторы событий, но действия для других сущностей выполняются таким же образом. Кроме того, в конце статьи приведены более подробные примеры, включая примеры с другими сущностями.

В этой части сценария мы создадим четыре дополнительных локальных переменных, которые будут использоваться для создания экземпляра объекта `EventHubDescription`. Сценарий выполняет следующее:

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

## Дальнейшие действия

В этой статье описана базовая схема подготовки к работе сущности служебной шины с помощью PowerShell. Все действия, которые можно выполнить с помощью клиентских библиотек .NET, также доступны для сценария PowerShell.

Более подробные примеры приведены в следующих записях блога:

- [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Как создать пространство имен и концентратор событий служебной шины с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Некоторые готовые сценарии также доступны для скачивания на странице [Сценарии PowerShell для служебной шины](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Варианты приобретения]: http://azure.microsoft.com/pricing/purchase-options/
[Предложения для участников]: http://azure.microsoft.com/pricing/member-offers/
[Бесплатное пробное использование]: http://azure.microsoft.com/pricing/free-trial/
[Установка и настройка Azure PowerShell]: ../powershell-install-configure.md
[пакете NuGet служебной шины]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET для служебной шины]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

<!---HONumber=AcomDC_0211_2016-->