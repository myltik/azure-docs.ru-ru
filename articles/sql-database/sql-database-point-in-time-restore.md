---
title: "Восстановление базы данных SQL Azure до точки во времени | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure до точки во времени"
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
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Восстановление базы данных SQL Azure до точки во времени 

В этом практическом руководстве объясняется, как восстановить базу данных до предшествующей точки во времени из [автоматически созданных резервных копий базы данных SQL](sql-database-automated-backups.md). 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Восстановление до предшествующей точки во времени с помощью портала Azure

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных](sql-database-get-started-backup-recovery.md).
>

Выберите базу данных для восстановления на портале Azure.

1. Откройте [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **Больше служб** > **Базы данных SQL**.
3. Щелкните по базе данных, которую хотите восстановить.
4. В верхней части страницы базы данных выберите **Восстановить**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. На странице **Восстановление** выберите дату и время (в формате UTC) для восстановления базы данных. Нажмите кнопку **ОК**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Нажав кнопку **ОК** на предыдущем шаге, щелкните значок уведомления в правом верхнем углу страницы. Щелкните уведомление **Идет восстановление базы данных SQL**, чтобы получить дополнительные сведения.
   
    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. Откроется страница "Идет восстановление базы данных SQL", содержащая сведения о состоянии восстановления. Для получения более подробных сведений можно щелкнуть интересующую вас строку.
   
    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

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
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md)
* Чтобы просмотреть самую старую точку восстановления из резервных копий базы данных, создаваемых службой, ознакомьтесь со статьей [Просмотр самой старой точки восстановления из резервных копий базы данных SQL Azure, создаваемых службой](sql-database-view-oldest-restore-point.md).
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


