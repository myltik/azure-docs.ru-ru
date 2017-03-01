---
title: "PowerShell: восстановление базы данных SQL Azure до точки во времени | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure до предшествующей точки во времени с помощью PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 5e5f0a474b6a34581be804b5d18d6ae03c99c14d
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell

В этой статье объясняется, как восстановить базу данных до предшествующей точки времени из [созданных автоматически резервных копий базы данных SQL](sql-database-automated-backups.md) с помощью PowerShell. Эту задачу можно также выполнить на [портале Azure](sql-database-point-in-time-restore-portal.md).  

## <a name="restore-to-a-previous-point-in-time"></a>Восстановление до предшествующей точки во времени 

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных](sql-database-get-started-backup-recovery-powershell.md).
>


## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>Восстановление до предшествующей точки во времени с помощью PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>Восстановление базы данных до точки во времени в качестве отдельной базы данных
1. Получите базу данных, которую необходимо восстановить, используя командлет [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Восстановите базу данных до точки во времени, используя командлет [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>Восстановление базы данных до точки во времени в пул эластичных баз данных
1. Получите базу данных, которую необходимо восстановить, используя командлет [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Восстановите базу данных до точки во времени, используя командлет [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [создаваемых автоматически резервных копий базы данных SQL](sql-database-automated-backups.md)
* Чтобы узнать об использовании создаваемых автоматически резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md)
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)


