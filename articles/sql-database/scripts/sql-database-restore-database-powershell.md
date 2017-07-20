---
title: "Пример для PowerShell. Восстановление и архивация базы данных SQL Azure | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для восстановления базы данных SQL Azure из геоизбыточных резервных копий."
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2be93bd623ae3960c61d54d4219cc4b60257f88d
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017

---

# <a name="use-powershell-to-restore-an-azure-sql-database-from-backups"></a>Восстановление базы данных SQL Azure из резервных копий с помощью PowerShell

Этот пример сценария PowerShell восстанавливает базу данных SQL Azure из геоизбыточной резервной копии, восстанавливает последнюю резервную копию удаленной базы данных SQL Azure и восстанавливает базу данных SQL Azure до определенной точки во времени.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Создание базы данных SQL")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Создает логический сервер, на котором размещена база данных или эластичный пул. | 
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Создает на логическом сервере отдельную базу данных или базу данных в составе пула. |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | Получает геоизбыточную резервную копию базы данных. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | Восстанавливает базу данных SQL. |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | Удаляет базу данных SQL Azure. |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Получает удаленную базу данных, которую можно восстановить. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).

