---
title: "Использование командлетов PowerShell с Azure RemoteApp | Документация Майкрософт"
description: "Узнайте, как использовать командлеты Windows PowerShell в Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dcd220da143b518bf28cc1169ab68d20256c07ba


---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Использование командлетов Windows PowerShell с Azure RemoteApp
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

 Для администрирования и обслуживания коллекций можно использовать командлеты PowerShell для Azure RemoteApp. Используйте следующие сведения, чтобы приступить к работе.

## <a name="get-the-cmdlets"></a>Загрузка командлетов
- - -
Сначала скачайте командлеты Azure PowerShell [отсюда](http://go.microsoft.com/?linkid=9811175). В их состав уже входят командлеты RemoteApp. 

Изучите [справку по командлетам Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Настройка командлетов Azure для работы с подпиской
- - -
С помощью [этого руководства](../powershell-install-configure.md) вы сможете сделать так, чтобы командлеты можно было использовать с вашей подпиской Azure.

Чтобы быстро приступить к работе:

1. Скачайте и установите [командлеты Azure PowerShell](http://go.microsoft.com/?linkid=9811175).
2. Запустите Microsoft Azure PowerShell.
3. Выполните команду **Add-AzureAccount** для аутентификации в своей подписке Azure. При появлении запроса введите имя пользователя и пароль, которые используются для входа на портал Azure.  
4. Выполните команду **Get-AzureSubscription** , чтобы получить список подписок, связанных с вашей учетной записью. 
5. Выполните команду **Select-AzureSubscription** и укажите имя или идентификатор подписки, которые будут использоваться в консоли PowerShell.

Поздравляем, консоль Azure PowerShell настроена и готова к работе. Имейте в виду, что вам потребуется повторять шаги 2–5 при каждом запуске консоли Azure PowerShell.  

## <a name="create-a-cloud-collection"></a>Создание облачной коллекции
- - -
Это просто. Выполните следующую команду:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

Приведенная выше команда автоматически публикует приложения Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio и Word).

Для создания коллекции может потребоваться 30 или более минут. В связи с этим команда возвращает идентификатор для отслеживания, который можно использовать следующим образом:

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

После создания коллекции в нее можно добавить пользователей с помощью следующей команды:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Готово! Этот пользователь сможет подключиться к приложению с помощью клиента Azure RemoteApp, который можно найти [здесь](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Доступные командлеты
Доступно множество других команд, документация для которых скоро будет опубликована.

Основные командлеты для коллекций RemoteApp 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

Командлеты для виртуальных сетей RemoteApp:

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

Командлеты для шаблонов образов RemoteApp:

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

Другие командлеты RemoteApp:

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult




<!--HONumber=Nov16_HO3-->


