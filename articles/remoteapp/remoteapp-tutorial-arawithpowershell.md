<properties
   pageTitle="Начало работы с Azure RemoteApp с помощью PowerShell"
   description="Инструкции по началу работы с платформой Azure RemoteApp с помощью командной оболочки PowerShell"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="07/07/2015"
   ms.author="guscatal;spatnaik;elizapo"/>



# Начало работы с Azure RemoteApp с помощью PowerShell
=====================================


## Загрузка командлетов 
-------------
Сначала необходимо загрузить командлеты PowerShell для Azure [здесь](http://go.microsoft.com/?linkid=9811175); в их состав уже входят командлеты для RemoteApp.

## Настройка командлетов Azure для работы с подпиской
------------------
С помощью [этого руководства](../powershell-install-configure.md) вы сможете сделать так, чтобы командлеты можно было использовать с вашей подпиской Azure.

## Создание облачной коллекции
--------------------
Это просто. Выполните следующую команду:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

Приведенная выше команда автоматически публикует приложения Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio и Word).

Для создания коллекции может потребоваться 30 или более минут. В связи с этим команда возвращает идентификатор для отслеживания, который можно использовать следующим образом:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

После создания коллекции в нее можно добавить пользователей с помощью следующей команды:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Готово! Этот пользователь сможет подключиться к приложению с помощью клиента Azure RemoteApp, который можно найти [здесь](https://www.remoteapp.windowsazure.com/).

## Доступные командлеты
Доступно множество других команд, документация для которых скоро будет опубликована.

Основные командлеты для коллекций RemoteApp

- New-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Update-AzureRemoteAppCollection
- Remove-AzureRemoteAppCollection
- Add-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Remove-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnect-AzureRemoteAppSession
- Invoke-AzureRemoteAppSessionLogoff
- Send-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publish-AzureRemoteAppProgram
- Unpublish-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Командлеты для виртуальных сетей RemoteApp:

- New-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remove-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get-- AzureRemoteAppVpnDeviceConfigScript
- Reset-AzureRemoteAppVpnSharedKey

Командлеты для шаблонов образов RemoteApp:

- New-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rename-AzureRemoteAppTemplateImage
- Remove-AzureRemoteAppTemplateImage

Другие командлеты RemoteApp:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 

<!---HONumber=July15_HO5-->