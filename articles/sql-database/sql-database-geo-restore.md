---
title: "Восстановление базы данных SQL Azure из геоизбыточной резервной копии | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure на новый сервер из геоизбыточной резервной копии с помощью портала Azure или PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Восстановление базы данных SQL Azure из геоизбыточной резервной копии

В этой статье показано, как восстановить базу данных на новый сервер с помощью геовосстановления. Это можно сделать с помощью портала Azure или PowerShell.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure

Чтобы выполнить геовосстановление базы данных на портале Azure, выполните следующие действия.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **+Создать** > **Базы данных** > **База данных SQL**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Выберите **Резервная копия** в качестве источника, затем выберите резервную копию, из которой вы хотите восстановить базу данных. Укажите имя базы данных, сервер, на котором вы хотите восстановить базу данных, и нажмите кнопку **Создать**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Чтобы отслеживать состояние операции восстановления, можно щелкнуть значок уведомления в правом верхнем углу страницы.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>Геовосстановление базы данных в автономную базу данных
1. Получите геоизбыточную резервную копию базы данных, которую необходимо восстановить, с помощью командлета [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Запустите восстановление из геоизбыточной резервной копии с помощью командлета [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>Геовосстановление базы данных в пул эластичных баз данных
1. Получите геоизбыточную резервную копию базы данных, которую необходимо восстановить, с помощью командлета [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Запустите восстановление из геоизбыточной резервной копии с помощью командлета [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Укажите имя пула, в который необходимо восстановить базу данных.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь со статьей [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).  
* Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).




<!--HONumber=Dec16_HO3-->


