---
title: Управление долгосрочным хранением резервных копий базы данных SQL Azure | Документация Майкрософт
description: Узнайте, как сохранять автоматически создаваемые резервные копии в хранилище SQL Azure и восстанавливать их.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 0ce22cae50e70ca7232e025d4009b23d62f6a198
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649233"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Управление долгосрочным хранением резервных копий базы данных SQL Azure

Вы можете настроить базу данных SQL Azure с политикой [долгосрочного хранения резервных копий](sql-database-long-term-retention.md) (LTR) таким образом, чтобы обеспечивалось автоматическое хранение больших двоичных объектов в Azure на протяжении 10 лет. После этого появляется возможность восстановления базы данных с помощью резервных копий на портале Azure или через PowerShell.

> [!NOTE]
> Как составная часть начальной версии пробного выпуска этой функции, вышедшей в октябре 2016 г., резервные копии хранились в хранилище службы восстановления служб Azure. Данное обновление устраняет такую зависимость, но для обеспечения обратной совместимости поддержка исходного API сохраняется до 31 мая 2018 г. Сведения о взаимодействии с резервными копиями в хранилище службы восстановления служб Azure см. в статье [Configure and restore from Azure SQL Database long-term backup retention using Azure Recovery Services Vault](sql-database-long-term-backup-retention-configure-vault.md) (Настройка и восстановление с использованием долгосрочного хранения резервных копий базы данных SQL Azure с помощью хранилища служб восстановления Azure). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Настройка политик долгосрочного хранения и восстановление резервных копий с помощью портала Azure

В следующих разделах показано, как настроить долгосрочное хранение, просматривать резервные копии с моделью долгосрочного хранения, а также восстанавливать резервные копии из этой модели на портале Azure.

### <a name="configure-long-term-retention-policies"></a>Настройка политик долгосрочного хранения

Вы можете настроить базу данных SQL для [сохранения автоматически создаваемых резервных копий](sql-database-long-term-retention.md) на срок больший, чем срок хранения для вашего уровня службы. 

1. На портале Azure выберите свой сервер SQL и нажмите кнопку **Долгосрочное хранение архивных копий**.

   ![Ссылка Long-term backup retention (Долгосрочное хранение резервных копий)](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. На вкладке **Configure policies** (Настройка политик) выберите базу данных, для которой требуется задать или изменить политики долгосрочного хранения резервных копий.

   ![Выбор базы данных](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. На панели **Configure policies** (Настройка политик) выберите сохранение еженедельных, ежемесячных или ежегодных резервных копий и укажите срок хранения для каждого из вариантов. 

   ![Настройка политик](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. По завершении операций нажмите кнопку **Применить**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Просмотр резервных копий и восстановление из резервной копии с помощью портала Azure

Просмотрите резервные копии, которые сохраняются в указанной базе данных под управлением политики долгосрочного хранения (LTR), и выполните восстановление из этих резервных копий. 

1. На портале Azure выберите свой сервер SQL и нажмите кнопку **Долгосрочное хранение архивных копий**.

   ![Ссылка Long-term backup retention (Долгосрочное хранение резервных копий)](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. На вкладке **Доступные архивы** выберите базу данных, для которой нужно просмотреть доступные резервные копии.

   ![Выбор базы данных](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. На панели **Доступные архивы** просмотрите доступные резервные копии. 

   ![Просмотр резервных копий](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Выберите резервную копию, на основе которой требуется выполнить восстановление, и задайте новое название базы данных.

   ![Восстановление](./media/sql-database-long-term-retention/ltr-restore.png)

5. Нажмите кнопку **ОК**, чтобы восстановить базу данных из резервной копии, расположенной в хранилище SQL Azure, в новую базу данных.

6. На панели инструментов щелкните значок уведомления, чтобы просмотреть состояние задания восстановления.

   ![Ход выполнения задания восстановления из хранилища](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. После завершения задания откройте страницу **Базы данных SQL**, чтобы просмотреть восстановленную базу данных.

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных, или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Настройка политик долгосрочного хранения и восстановление резервных копий с помощью PowerShell

В следующем разделе показано, как настроить долгосрочное хранение резервных копий, просматривать резервные копии в хранилище SQL Azure и выполнять восстановление из резервной копии в хранилище SQL Azure с помощью PowerShell.

> [!IMPORTANT]
> Чтобы установить политики LTR версии 2, необходимо использовать последнюю версию PowerShell для AzureRM. Текущая версия — [AzureRM 4.5.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0-preview). Это предварительная версия. Чтобы ее установить, выполните следующую команду: `Install-Module -Name AzureRM.Sql -AllowPrerelease -Force`.
> Сведения об установке предварительной версии см. в статье [Установка PowerShellGet](https://docs.microsoft.com/en-us/powershell/gallery/installing-psget). Версия PowerShell для AzureRM за май 2018 г. выпущена несколько дней назад (18.05.2018). Вы можете не использовать параметр -AllowPrelease при установке доступной версии этого выпуска. Вместо этого выполните следующую команду: `Install-Module -Name AzureRM.Sql -Force`.

### <a name="create-an-ltr-policy"></a>Создание политики LTR

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Просмотр политик LTR
В этом примере показан процесс выведения списка политик LTR на сервере.

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Удаление политики LTR
В этом примере показано, как удалить политику LTR из базы данных.

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Просмотр резервных копий LTR

В этом примере показано, как вывести список резервных копий LTR на сервере. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Удаление резервных копий LTR

В этом примере показано, как удалить резервную копию LTR из списка резервных копий.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Восстановление из резервных копий LTR
В этом примере показано, как выполнить восстановление из резервной копии LTR. Обратите внимание, что интерфейс не меняется, но параметр идентификатора ресурса теперь требует наличия идентификатора ресурса резервной копии LTR. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных. Ознакомьтесь с [восстановлением до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](sql-database-automated-backups.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
