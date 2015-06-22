<properties
	pageTitle="Управление Service Bus с помощью PowerShell"
	description="Управление Service Bus с помощью сценариев PowerShell вместо .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>

# Управление Service Bus с помощью PowerShell

## Обзор

Microsoft Azure PowerShell - это среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье вы узнаете, как использовать PowerShell для подготовки и управления такими сущностями Service Bus, как пространства имен, очереди и концентраторы событий, с помощью локальной консоли Azure PowerShell.

## Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure - это платформа на основе подписок. Дополнительную
информацию о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options],
[Предложения для участников][azure-member-offers] или [Бесплатная пробная версия][azure-free-trial].

- Компьютер с Azure PowerShell. Указания см. в разделе [Установка и настройка Azure PowerShell][Powershell-install-configure].

- Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## Добавление ссылки на сборку .NET для Service Bus

Для управления Service Bus доступно ограниченное число командлетов PowerShell. Для подготовки
сущностей, которые недоступны через существующие командлеты, мы воспользуемся клиентом .NET для
Service Bus с помощью [пакета NuGet для Service Bus][].

Сначала убедитесь в том, что используемый сценарий может найти сборку **Microsoft.ServiceBus.dll**, которая устанавливается вместе с пакетом NuGet. Чтобы обеспечить гибкость, сценарий выполнит следующие действия:

1. Определит путь, по которому он был вызван.
2. Выполнит рекурсию по пути, пока не будет найдена папка с именем  `packages`. Эта папка создается во время установки пакетов NuGet.
3. Выполнит рекурсивный поиск в папке  `packages` для сборки с именем **Microsoft.ServiceBus.dll**.
4. Создаст ссылки на сборку, чтобы имеющиеся типы были доступны для дальнейшего использования.

В сценарии PowerShell эти действия выполняются следующим образом:

```powershell

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

При работе с пространством имен Service Bus вместо пакета SDK для .NET можно использовать два командлета. А именно, можно использовать [Get-AzureSBNamespace][] и [New-AzureSBNamespace][].

В нашем примере мы создадим несколько локальных переменных в сценарии: `$Namespace` и `$Location`.
- `$Namespace` - имя пространства имен Service Bus, которое будет использоваться для работы.
- `$Location` - центр обработки данных, в котором пространство имен будет подготовлено к работе.
- `$CurrentNamespace` - место хранения полученного (или созданного) исходного пространства имен.

В фактическом сценарии переменные `$Namespace` и `$Location` могут передаваться как параметры.

Эта часть сценария:

1. Попытается получить пространство имен Service Bus с заданным именем.
2. Если пространство имен будет найдено, отобразится его имя.
3. Если пространство имен не удастся найти, оно будет создано и затем получено сценарием.

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

Чтобы подготовить другие сущности Service Bus к работе, создадим экземпляр объекта  `NamespaceManager` из пакета SDK.
Чтобы получить правило авторизации, которое позволяет указать строку подключения, можно использовать командлет [Get AzureSBAuthorizationRule][]. Ссылка на экземпляр  `NamespaceManager` будет сохранена в переменной `$NamespaceManager` . В дальнейшем мы используем переменную `$NamespaceManager` в сценарии для подготовки к работе других сущностей.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Подготовка других сущностей Service Bus

Чтобы подготовить к работе другие сущности, такие как очереди, разделы и концентраторы событий, можно использовать [API .NET для Service Bus][dotnet-servicebus-api]. В этой статье мы рассмотрим только концентраторы событий, но подготовка к работе других сущностей осуществляется таким же образом. Кроме того, в конце статьи приведены более подробные примеры, включая примеры с другими сущностями.

В этой части сценария мы создадим четыре дополнительных локальных переменных, которые будут использоваться для создания экземпляра `EventHubDescription`. Мы выполним следующее:

1. Сначала с помощью объекта `NamespaceManager` проверим, существует ли концентратор событий, определенный `$Path`.
2. Если он не существует, мы создадим `EventHubDescription` и передадим его в класс `NamespaceManager` метода `CreateEventHubIfNotExists`.
3. Когда концентратор событий станет доступен, мы создадим группу потребителей с помощью `ConsumerGroupDescription` и `NamespaceManager`.

``` powershell

$Path  = "MyEventHub"
$PartitionCount = 12
$MessageRetentionInDays = 7
$UserMetadata = $null
$ConsumerGroupName = "MyConsumerGroup"

# Check if the event hub already exists
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

В этой статье описана базовая схема подготовки к работе сущности Service Bus с помощью PowerShell. Все действия, которые можно выполнить с помощью клиентских библиотек .NET, также доступны для сценария PowerShell.

Более подробные примеры приведены в следующих записях блога:

- [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Как создать пространство имен и концентратор событий Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Некоторые готовые сценарии доступны для скачивания на странице:
- [Сценарии PowerShell для Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[powershell-install-configure]: ../install-configure-powershell/
[пакета NuGet для Service Bus]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[dotnet-servicebus-api]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!--HONumber=47-->
 