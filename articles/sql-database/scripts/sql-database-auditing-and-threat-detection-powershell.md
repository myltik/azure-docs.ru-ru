---
title: "Сценарий Azure PowerShell. Настройка аудита и обнаружения угроз для базы данных | Документация Майкрософт"
description: "Сценарий Azure PowerShell для настройки аудита и обнаружения угроз для базы данных SQL."
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
ms.openlocfilehash: 736da582caea53d236add0fdb6ebd46f9a58b64a
ms.lasthandoff: 03/30/2017

---

# <a name="configure-sql-database-auditing-and-threat-detection-using-powershell"></a>Настройка аудита и обнаружения угроз для базы данных SQL с помощью PowerShell

Этот пример сценария Azure PowerShell позволяет настроить аудит и обнаружение угроз для базы данных SQL. 

[!INCLUDE [sample-cli-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1 "Включение аудита и обнаружения угроз")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Создает логический сервер, на котором размещена база данных или эластичный пул. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Создает на логическом сервере отдельную базу данных или базу данных в составе пула. |
| [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.4.0/new-azurermstorageaccount) | Создает учетную запись хранения. |
| [Set-AzureRmSqlDatabaseAuditingPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabaseauditingpolicy) | Задает политику аудита для базы данных. |
| [Set-AzureRmSqlDatabaseThreatDetectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabasethreatdetectionpolicy) | Задает политику обнаружения угроз для базы данных. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).

