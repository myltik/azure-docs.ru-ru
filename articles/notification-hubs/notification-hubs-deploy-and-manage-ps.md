<properties 
	pageTitle="Развертывание и управление центров уведомлений с помощью PowerShell" 
	description="Создание и управление центрами уведомлений с помощью PowerShell в целях автоматизации" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Развертывание и управление центров уведомлений с помощью PowerShell

##Обзор

В этой статье рассказывается, как создать и управлять центрами уведомлений Azure с помощью PowerShell. В этом разделе рассматриваются следующие общие задачи автоматизации.

+ Создание центра уведомлений
+ Настройка учетных данных

Если необходимо создать новое пространство имен служебной шины для ваших центров уведомлений, см. [Управление Service Bus с помощью PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

Управление центрами уведомлений не поддерживается напрямую с помощью командлетов, включенных в Azure PowerShell. Лучше всего из PowerShell указать сборку Microsoft.ServiceBus.dll. Сборка входит в состав [пакета NuGet для служебной шины](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).


## Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения], [Предложения для участников] или [Бесплатное пробное использование].

- Компьютер с Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell].

- Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.


## Добавление ссылки на сборку .NET для Service Bus

Управление центрами уведомлений Azure еще не включено в командлеты PowerShell в Azure PowerShell. Для подготовки центров уведомлений и других сущностей служебной шины, которые недоступны через существующие командлеты, можно использовать клиент .NET для служебной шины в [пакете NuGet для служебной шины](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

Сначала убедитесь, что сценарий может найти сборку **Microsoft.ServiceBus.dll**, которая устанавливается как пакет NuGet в проекте Visual Studio. Для гибкости сценарий выполняет такие действия:

1. Определяет путь, по которому он был вызван.
2. Проходит по пути и находит папку с именем `packages`. Эта папка создается при установке пакетов NuGet для проектов Visual Studio.
3. Рекурсивно ищет в папке `packages` сборку с именем **Microsoft.ServiceBus.dll**.
4. Создает ссылку на сборку, чтобы типы стали доступны для дальнейшего использования.

В сценарии PowerShell эти действия выполняются следующим образом:

``` powershell

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

## Создание класса NamespaceManager

Для подготовки центров уведомлений и других сущностей служебной шины создайте экземпляр класса [NamespaceManager](http://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx) из пакета SDK.

Получить правило авторизации для указания строки подключения можно с помощью командлета [Get-AzureSBAuthorizationRule] в составе Azure PowerShell. Ссылка на экземпляр `NamespaceManager` будет сохранена в переменной `$NamespaceManager`. Для подготовки центра уведомлений будет использоваться `$NamespaceManager`.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## Подготовка нового центра уведомлений 

Для подготовки нового центра уведомлений используйте [API .NET для служебной шины]. Данная статья целиком посвящена центрам уведомлений. Для работы с другими сущностями служебной шины см. [Управление служебной шиной с помощью PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

В этой части сценария выполняется настройка четырех локальных переменных.

1. `$Namespace`: присвоить данное значение имени пространства имен, в котором нужно создать центр уведомлений.
2. `$Path`: присвоить путь имени нового центра уведомлений. Например, MyHub.    
3. `$WnsPackageSid`: присвоить данное значение пакету SID для своего приложения Windows из [Центра разработок для Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: присвоить данное значение секретному ключу для своего приложения Windows из [Центра разработок для Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Эти переменные используются для подключения к своему пространству имен служебной шины и создания нового центра уведомлений, настроенного на обработку уведомлений от служб уведомлений Windows (WNS) с использованием учетных данных WNS для приложения Windows. Сведения о получении пакета SID и секретного ключа см. в учебнике [Приступая к работе с центрами уведомлений](notification-hubs-windows-store-dotnet-get-started.md).

+ Фрагмент сценария использует объект `NamespaceManager` для проверки существования центра уведомлений, идентифицируемого посредством `$Path`.

+ Если его не существует, сценарий создает `NotificationHubDescription` по учетным данным WNS и передает его в класс `NamespaceManager` метод `CreateNotificationHub`.

``` powershell

$Namespace = "<Enter your namespace>
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.ServiceBus.Notifications.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.ServiceBus.Notifications.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## Дополнительные ресурсы

- [Управление служебной шиной с помощью PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
- [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Как создать пространство имен и концентратор событий служебной шины с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Некоторые готовые сценарии также доступны для скачивания на странице [Сценарии PowerShell для служебной шины](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Варианты приобретения]: http://azure.microsoft.com/pricing/purchase-options/
[Предложения для участников]: http://azure.microsoft.com/pricing/member-offers/
[Бесплатное пробное использование]: http://azure.microsoft.com/pricing/free-trial/
[Установка и настройка Azure PowerShell]: ../install-configure-powershell.md
[API .NET для служебной шины]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 

<!---HONumber=August15_HO6-->