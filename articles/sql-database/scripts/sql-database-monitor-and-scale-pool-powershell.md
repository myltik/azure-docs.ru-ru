---
title: "Сценарий Azure PowerShell. Мониторинг и масштабирование эластичного пула SQL | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для мониторинга и масштабирования эластичного пула баз данных SQL."
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
ms.openlocfilehash: dd1866368301f75f2df82a5b775d9c7ae0eafb44
ms.lasthandoff: 03/30/2017

---

# <a name="monitor-and-scale-a-sql-database-elastic-pool-using-powershell"></a>Мониторинг и масштабирование эластичного пула баз данных SQL с помощью PowerShell

Этот пример сценария PowerShell отслеживает метрики производительности эластичного пула, масштабирует его до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1 "Мониторинг и масштабирование отдельной базы данных SQL")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Создает логический сервер, на котором размещена база данных или эластичный пул. |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlelasticpool) | Создает эластичный пул на логическом сервере. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Создает на логическом сервере отдельную базу данных или базу данных в составе пула. |
| [Get-AzureRmMetric](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/get-azurermmetric) | Отображает сведения об используемом размере базы данных.|
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | Добавляет или обновляет правило генерации оповещений на основе метрики. |
| [Set-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqlelasticpool) | Обновляет свойства эластичного пула. |
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | Задает правило генерации оповещений для автоматического отслеживания единиц DTU в будущем. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).

