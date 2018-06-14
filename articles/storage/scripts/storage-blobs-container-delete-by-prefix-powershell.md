---
title: Пример скрипта PowerShell. Удаление контейнеров на основе префикса | Документация Майкрософт
description: Сведения об удалении контейнеров больших двоичных объектов службы хранилища Azure на основе префикса имени.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 629189b9dbe2327763d364abc95f49539a312c53
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
ms.locfileid: "25983904"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Удаление контейнеров на основе префикса имени

Приведенный в этой статье скрипт удаляет контейнеры в хранилище BLOB-объектов Azure на основе определенного префикса имени.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните команду ниже, чтобы удалить группу ресурсов, оставшиеся контейнеры и все связанные ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Описание скрипта

Ниже приведены команды, с помощью которых скрипт удаляет контейнеры на основе префикса имени. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Возвращает определенную учетную запись хранения или все учетные записи хранения в группе ресурсов или подписке. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Возвращает список контейнеров хранилища, связанных с учетной записью хранения. |
| [Remove-AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Удаляет указанный контейнер хранилища. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).
