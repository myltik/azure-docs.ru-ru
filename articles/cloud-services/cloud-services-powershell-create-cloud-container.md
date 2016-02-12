<properties
   pageTitle="Использование команды Azure PowerShell для создания пустого контейнера облачной службы | Microsoft Azure"
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
В этой статье объясняется, как быстро создать контейнер облачных служб с помощью командлетов Azure PowerShell. Выполните следующие действия.

1. Установите командлет Microsoft Azure PowerShell со страницы [Загрузки Azure PowerShell](http://aka.ms/webpi-azps).
2. Запустите командную строку PowerShell.
3. Войдите, используя [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx).

    > [AZURE.NOTE] Дополнительные указания по установке командлета Azure PowerShell и подключению к подписке Azure см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

4. Используйте командлет **New-AzureService**, чтобы создать пустой контейнер облачной службы Azure.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Ниже приводится пример вызова командлета: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

Чтобы получить дополнительные сведения о создании облачной службы Azure, выполните следующую команду: ```
Get-help New-AzureService
```

### Дальнейшие действия

 * Чтобы управлять развертыванием облачной службы, используйте команды [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) и [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Кроме того, дополнительные сведения можно получить в статье [Настройка облачных служб](cloud-services-how-to-configure.md).

 * Чтобы опубликовать проект облачной службы в Azure, используйте образец кода **PublishCloudService.ps1** из статьи [Непрерывная доставка для облачных служб в Azure](cloud-services-dotnet-continuous-delivery.md).

<!---HONumber=AcomDC_0204_2016-->