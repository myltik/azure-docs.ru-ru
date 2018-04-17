---
title: Пример для CLI. Мониторинг и масштабирование отдельной базы данных SQL Azure | Документация Майкрософт
description: Пример сценария Azure PowerShell для отслеживания и масштабирования отдельной базы данных SQL Azure.
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: adb2da80ecf0b0c8feb7ebd242b1e98a871aef77
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>Мониторинг и масштабирование отдельной базы данных SQL с помощью PowerShell

Этот пример сценария PowerShell отслеживает метрики производительности базы данных, масштабирует ее до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=13-14 "Monitor and scale single SQL Database")]

> [!TIP]
> Сведения о состоянии операции с базой данных можно получить при помощи [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity). Чтобы отменить операцию обновления в базе данных, воспользуйтесь [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Создает логический сервер, на котором размещена база данных или эластичный пул. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Отображает сведения об используемом размере базы данных.|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Обновляет свойства базы данных или перемещает базу данных в эластичный пул, из него или между эластичными пулами. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Задает правило генерации оповещений для автоматического отслеживания единиц DTU в будущем. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
