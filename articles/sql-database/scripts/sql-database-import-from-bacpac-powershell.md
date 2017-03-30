---
title: "Сценарий Azure PowerShell. Импорт BACPAC-файла в базу данных SQL | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для импорта BACPAC-файла в базу данных SQL."
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 0afe2d9e8cb6d26c43830df0ebfe2cea5c2f7665
ms.lasthandoff: 03/30/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>Импорт данных из BACPAC-файла в базу данных SQL с помощью PowerShell

Этот пример сценария PowerShell импортирует базу данных из BACPAC-файла.  

[!INCLUDE [sample-cli-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "Создание базы данных SQL")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup]() | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmSqlServer]() | Создает логический сервер, на котором размещена база данных SQL. |
| [New-AzureRmSqlServerFirewallRule]() | Создает правило брандмауэра, чтобы разрешить доступ ко всем базам данных SQL на сервере по введенному диапазону IP-адресов. |
| [New-AzureRmSqlDatabase]() | Создает базу данных SQL на логическом сервере. |
| [Remove-AzureRmResourceGroup]() | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
