<properties
   pageTitle="Использование команды Azure PowerShell для создания пустого контейнера облачной службы"
   description="В этой статье объясняется, как создать контейнер облачной службы и выполнить операции управления, связанные с облачными службами, с помощью сценария PowerShell"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="01/13/2015"
   ms.author="cawa"/>

# Использование команды Azure PowerShell для создания пустого контейнера облачной службы
1. Установите командлет Microsoft Azure PowerShell из [загрузки Azure PowerShell](http://aka.ms/webpi-azps). Запустите командную строку PowerShell. Используйте [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) для входа

> [AZURE.NOTE]Дополнительные указания по установке командлета Azure PowerShell и подключения к подписке Azure см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

2. **New-AzureService** — это командлет, который создает пустой контейнер облачной службы.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Пример вызова командлета: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Для получения дополнительной информации о создании облачной службы Azure запустите: ```
Get-help New-AzureService
```

4. Дальнейшие действия:

   - Чтобы управлять развертыванием облачной службы, используйте команды [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) и [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Также см. раздел [Настройка облачных служб](cloud-services-how-to-configure.md)

    - Чтобы опубликовать проект облачной службы в Azure, используйте образец кода **PublishCloudService.ps1** из [непрерывной доставки для облачных служб в Azure](cloud-services-dotnet-continuous-delivery.md)
 

<!---HONumber=AcomDC_0114_2016-->