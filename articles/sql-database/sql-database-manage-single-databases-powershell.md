---
title: "Создание и администрирование отдельных баз данных Azure SQL с помощью PowerShell | Документация Майкрософт"
description: "Краткий справочник по созданию и администрированию отдельных баз данных SQL Azure с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>Создание и администрирование отдельных баз данных Azure SQL с помощью PowerShell

Вы можете создавать и администрировать отдельные базы данных SQL Azure с помощью [портала Azure](https://portal.azure.com/), PowerShell, Transact-SQL, REST API или языка C#. Этот раздел посвящен использованию PowerShell. Портал Azure: см. дополнительные сведения о [создании и администрировании отдельных баз данных с помощью портала Azure](sql-database-manage-single-databases-powershell.md). Transact-SQL: см. дополнительные сведения о [создании и администрировании отдельных баз данных с помощью Transact-SQL](sql-database-manage-single-databases-tsql.md). 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>Создание базы данных SQL Azure с помощью PowerShell

Чтобы создать базу данных SQL, используйте командлет [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). В подписке уже должны существовать группа ресурсов и сервер. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

> [!TIP]
> Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md).
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Изменение уровня служб и уровня производительности отдельной базы данных

Выполните командлет [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) и задайте в параметре **-RequestedServiceObjectiveName** уровень производительности требуемой ценовой категории, например *S0*, *S1*, *S2*, *S3*, *P1*, *P2* и т. д.

Замените ```{variables}``` собственными значениями (не включая фигурные скобки).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>Дальнейшие действия
* Обзор средств управления см. в статье [Обзор: средства управления для Базы данных SQL](sql-database-manage-overview.md).
* Чтобы узнать, как выполнять задачи управления с использованием портала Azure, ознакомьтесь со статьей [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).
* Чтобы узнать, как выполнять задачи управления с использованием PowerShell, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).
* Чтобы узнать, как выполнять задачи управления с использованием SQL Server Management Studio, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Сведения о службе Базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). 
* Сведения о серверах и функциях базы данных Azure см. в статье [Возможности базы данных SQL Azure](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


