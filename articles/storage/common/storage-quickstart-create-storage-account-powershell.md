---
title: "Краткое руководство Azure. Создание учетной записи хранения с помощью PowerShell | Документация Майкрософт"
description: "Быстро научитесь создавать учетную запись хранения с помощью PowerShell."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: tamram
ms.openlocfilehash: 87f179d2cf3802af3eca58e0adf57b07069bfcc6
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Создание учетной записи хранения с помощью PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командной строки PowerShell или сценариев. Это руководство содержит сведения о создании учетной записи хранения Azure с помощью PowerShell. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этим кратким руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Login-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```

Если вы не знаете, какое расположение нужно использовать, можно получить список доступных расположений. Получив список, найдите расположение, которое нужно использовать. В этом примере используется **eastus**. Сохраните его в переменной и используйте ее. Так вы сможете изменить расположение в одном месте.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-storage-account"></a>Создание учетной записи хранения общего назначения

Существует несколько типов учетных записей хранения в зависимости от того, как они будут использоваться и для какой службы (больших двоичных объектов, файлов, таблицы или очередей). В этой таблице перечислены возможности.

|**Тип учетной записи хранения**|**Общего назначения, класс "Стандартный"**|**Общего назначения, класс "Премиум"**|**Хранилище BLOB-объектов, "горячий" и "холодный" уровни доступа**|
|-----|-----|-----|-----|
|**Поддерживаемые службы**| Службы больших двоичных объектов, файлов, таблиц и очередей | Служба больших двоичных объектов | Служба больших двоичных объектов|
|**Типы поддерживаемых BLOB-объектов**|Блочные, страничные и добавочные BLOB-объекты | Blob-страницы | Блочные и добавочные BLOB-объекты|

Выполните команду [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), чтобы создать учетную запись хранения общего назначения, которую можно использовать для всех четырех служб. Задайте для учетной записи хранения имя *contosomvcstandard* и активируйте для нее локально избыточное хранилище и шифрование больших двоичных объектов.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). В этом случае будет удалена созданная вами учетная запись хранения.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали стандартную учетную запись хранения общего назначения. Сведения об отправке и скачивании больших двоичных объектов с помощью учетной записи хранения см. в кратком руководстве по хранилищу BLOB-объектов.
> [!div class="nextstepaction"]
> [Transfer objects to/from Azure Blob storage using Azure PowerShell](../blobs/storage-quickstart-blobs-powershell.md) (Передача объектов в хранилище BLOB-объектов Azure и обратно с помощью Azure PowerShell)