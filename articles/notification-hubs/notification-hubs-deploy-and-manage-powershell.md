---
title: Развертывание и управление центров уведомлений с помощью PowerShell
description: Создание и управление центрами уведомлений с помощью PowerShell в целях автоматизации
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d2350d8021925278d6362c8227d408476a569319
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776912"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Развертывание и управление центров уведомлений с помощью PowerShell
## <a name="overview"></a>Обзор
В этой статье рассказывается, как создать и управлять центрами уведомлений Azure с помощью PowerShell. В этом разделе рассматриваются следующие общие задачи автоматизации.

* Создание центра уведомлений
* Настройка учетных данных

Если необходимо создать новое пространство имен служебной шины для ваших центров уведомлений, см. статью [Управление служебной шиной с помощью PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Управление центрами уведомлений не поддерживается напрямую с помощью командлетов, включенных в Azure PowerShell. Лучше всего для PowerShell указать сборку Microsoft.Azure.NotificationHubs.dll. Сборка входит в состав [пакета NuGet для центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. на страницах [Как приобрести Azure], [Предложения для участников] или [Создайте бесплатную учетную запись Azure уже сегодня].
* Компьютер с Azure PowerShell. Инструкции см. в статье [Установка и настройка Azure PowerShell].
* Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Добавление ссылки на сборку .NET для Service Bus
Управление центрами уведомлений Azure еще не включено в командлеты PowerShell в Azure PowerShell. Для подготовки центров уведомлений можно использовать клиент .NET, содержащийся в [пакете NuGet для центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Сначала убедитесь, что сценарий может найти сборку **Microsoft.Azure.NotificationHubs.dll** , которая устанавливается как пакет NuGet в проекте Visual Studio. Для гибкости сценарий выполняет такие действия:

1. Определяет путь, по которому он был вызван.
2. Проходит по пути и находит папку с именем `packages`. Эта папка создается при установке пакетов NuGet для проектов Visual Studio.
3. Рекурсивно ищет в папке `packages` сборку с именем **Microsoft.Azure.NotificationHubs.dll**.
4. Создает ссылку на сборку, чтобы типы стали доступны для дальнейшего использования.

В сценарии PowerShell эти действия выполняются следующим образом:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Создание класса NamespaceManager
Для подготовки центров уведомлений создайте экземпляр класса [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) из пакета SDK. 

Получить правило авторизации для указания строки подключения можно с помощью командлета [Get-AzureSBAuthorizationRule] в составе Azure PowerShell. Ссылка на экземпляр `NamespaceManager` хранится в переменной `$NamespaceManager`. Для подготовки центра уведомлений используется `$NamespaceManager`.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Подготовка нового центра уведомлений
Для подготовки нового центра уведомлений используйте [API .NET для центров уведомлений].

В этой части сценария выполняется настройка четырех локальных переменных. 

1. `$Namespace` : присвойте имя пространства имен, в котором нужно создать центр уведомлений.
2. `$Path` : присвойте путь к имени нового центра уведомлений.  Например, MyHub.    
3. `$WnsPackageSid`: присвойте идентификатор безопасности пакета для своего приложения для Windows из [Центра разработки для Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: присвойте значение секретного ключа для своего приложения Windows из [Центра разработки для Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Эти переменные используются для подключения к пространству имен и создания нового центра уведомлений, настроенного для обработки уведомлений от служб уведомлений Windows (WNS) с использованием учетных данных WNS для приложения для Windows. Сведения о получении идентификатора безопасности пакета и секретного ключа см. в статье [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). 

* Фрагмент сценария использует объект `NamespaceManager` для проверки существования центра уведомлений, идентифицируемого по `$Path`.
* Если его не существует, сценарий создает `NotificationHubDescription` по учетным данным WNS и передает его в метод `CreateNotificationHub` класса `NamespaceManager`.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
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




## <a name="additional-resources"></a>Дополнительные ресурсы
* [Управление служебной шиной с помощью PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
* [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Как создать пространство имен и концентратор событий служебной шины с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Некоторые готовые сценарии доступны для скачивания:

* [Сценарии PowerShell для Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Как приобрести Azure]: http://azure.microsoft.com/pricing/purchase-options/
[Предложения для участников]: http://azure.microsoft.com/pricing/member-offers/
[Создайте бесплатную учетную запись Azure уже сегодня]: http://azure.microsoft.com/pricing/free-trial/
[Установка и настройка Azure PowerShell]: /powershell/azureps-cmdlets-docs
[API .NET для центров уведомлений]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx

