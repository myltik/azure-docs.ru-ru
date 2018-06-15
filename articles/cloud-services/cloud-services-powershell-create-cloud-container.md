---
title: Создание контейнера облачной службы с помощью PowerShell | Документация Майкрософт
description: В этой статье объясняется, как создать контейнер облачной службы с помощью PowerShell. В контейнере размещаются веб- и рабочие роли.
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: ''
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376139"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Использование команды Azure PowerShell для создания пустого контейнера облачной службы
В этой статье объясняется, как быстро создать контейнер облачных служб с помощью командлетов Azure PowerShell. Выполните следующие действия.

1. Установите командлет Microsoft Azure PowerShell со страницы [Загрузки Azure PowerShell](http://aka.ms/webpi-azps) .
2. Запустите командную строку PowerShell.
3. Войдите, используя [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) .

   > [!NOTE]
   > Инструкции по установке командлета Azure PowerShell и подключению к подписке Azure см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Используйте командлет **New-AzureService** , чтобы создать пустой контейнер облачной службы Azure.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Ниже приводится пример вызова командлета:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Чтобы получить дополнительные сведения о создании облачной службы Azure, выполните следующую команду:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Дополнительная информация
* Чтобы управлять развертыванием облачной службы, используйте команды [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0) и [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0). Кроме того, дополнительные сведения можно получить в статье [Настройка облачных служб](cloud-services-how-to-configure-portal.md) .
* Чтобы опубликовать проект облачной службы в Azure, используйте пример кода **PublishCloudService.ps1** из [репозитория заархивированных облачных служб](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
