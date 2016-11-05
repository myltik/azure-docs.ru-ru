---
title: Привязка SSL-сертификатов с помощью PowerShell
description: Узнайте, как привязать SSL-сертификаты к веб-приложению с помощью PowerShell.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: ahmedelnably

---
# Привязка SSL-сертификатов службы приложений Azure с помощью PowerShell
В выпуске Microsoft Azure PowerShell версии 1.1.0 был добавлен новый командлет, позволяющий привязывать существующие или новые SSL-сертификаты к существующему веб-приложению.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

См. дополнительные сведения об использовании [командлетов Azure PowerShell на основе Azure Resource Manager для управления веб-приложениями](app-service-web-app-azure-resource-manager-powershell.md).

## Передача и привязка нового SSL-сертификата
Сценарий: пользователь хочет привязать SSL-сертификат к одному из своих веб-приложений.

Зная имя группы ресурсов, в которую входит веб-приложение, имя веб-приложения, путь к PFX-файлу сертификата на компьютере пользователя, пароль к сертификату и пользовательское имя узла, можно создать привязку SSL-подключения с помощью следующей команды PowerShell:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Обратите внимание, что перед добавлением привязки SSL для веб-приложения необходимо настроить имя узла (пользовательский домен). Если имя узла не настроено, то при запуске командлета New-AzureRmWebAppSSLBinding будет выдана ошибка "Имя узла не существует". Имя узла можно добавить непосредственно с портала или с помощью Azure PowerShell. Настроить имя узла перед запуском командлета New-AzureRmWebAppSSLBinding можно с помощью следующего фрагмента PowerShell.

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

Важно понимать, что командлет Set-AzureRmWebApp перезаписывает имена узлов для веб-приложения. Таким образом, в приведенном выше фрагменте PowerShell производится добавление имен узлов для веб-приложения в существующий список.

## Передача и привязка существующего SSL-сертификата
Сценарий: пользователь хочет привязать загруженный ранее SSL-сертификат к одному из своих веб-приложений.

Получить список сертификатов, уже загруженных в определенную группу ресурсов, можно с помощью следующей команды:

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Обратите внимание, что сертификаты являются локальными и относятся в определенному расположению и группе ресурсов, так что в случае, если выбранное веб-приложение и необходимый сертификат находятся в разных расположениях и группах ресурсов, сертификат необходимо передать заново.

Зная имя группы ресурсов, в которую входит веб-приложение, имя веб-приложения, отпечаток сертификата и пользовательское имя узла, можно создать привязку SSL-подключения с помощью следующей команды PowerShell:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Удаление существующей привязки SSL-подключения
Сценарий: пользователь хочет удалить существующую привязку SSL-подключения.

Зная имя группы ресурсов, в которую входит веб-приложение, имя веб-приложения и пользовательское имя узла, можно удалить соответствующую привязку SSL-подключения с помощью следующей команды PowerShell:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Обратите внимание: если удаляемая привязка SSL-подключения является последней привязкой, использующей сертификат в указанном расположении, по умолчанию этот сертификат удаляется; для сохранения сертификата используйте параметр DeleteCertificate.

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Ссылки
* [Команды Azure PowerShell на основе Azure Resource Manager для веб-приложений Azure](app-service-web-app-azure-resource-manager-powershell.md)
* [Введение в среду службы приложения](app-service-app-service-environment-intro.md)
* [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->