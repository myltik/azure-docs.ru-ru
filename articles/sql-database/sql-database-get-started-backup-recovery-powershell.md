---
title: "Резервное копирование и восстановление базы данных SQL Azure с помощью PowerShell | Документация Майкрософт"
description: "Из этого руководства вы узнаете, как восстанавливать базы данных до точки во времени с помощью автоматически создаваемых резервных копий и сохранять автоматически создаваемые резервные копии в хранилище служб восстановления Azure. Также вы узнаете, как восстанавливать базы данных из хранилища служб восстановления Azure с помощью PowerShell."
keywords: "руководство по базе данных sql"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 93efe1a08149e7c027830b03a9e426ac5a05b27b
ms.openlocfilehash: 8a3ede8af471e656e830e38e0cf2f3a909fdaadb
ms.lasthandoff: 02/18/2017


---


# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-powershell"></a>Руководство по резервному копированию и восстановлению базы данных SQL Azure с помощью PowerShell

В этом руководстве мы помощью PowerShell выполним следующие операции:

- просмотрим имеющиеся резервные копии базы данных;
- восстановим базу данных до предыдущей точки во времени;
- настроим долгосрочное хранение файла резервной копии базы данных в хранилище служб восстановления Azure;
- восстановим базу данных из хранилища служб восстановления Azure.

**Оценка времени.** Для работы с этим руководством требуется около 30 минут (при условии, что предварительные требования уже выполнены).


## <a name="prerequisites"></a>Предварительные требования

* Вам понадобится учетная запись Azure. Вы можете [создать бесплатную учетную запись Azure](https://azure.microsoft.com/free/) или [активировать преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Вам нужно подключиться к Azure, используя учетную запись, которой назначена роль владельца или участника подписки. Дополнительные сведения об управлении доступом на основе ролей (RBAC) см. в статье [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md).

* Вам нужно установить и запустить последнюю версию Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

* Вам нужно изучить руководство [по началу работы с серверами баз данных SQL Azure, базами данных и правилами брандмауэра с использованием портала Azure, SQL Server Management Studio](sql-database-get-started.md) или [PowerShell](sql-database-get-started-powershell.md). Либо выполните скрипт PowerShell, приведенный в конце версии этого руководства по использованию [PowerShell](sql-database-get-started-powershell.md), прежде чем продолжать.

> [!TIP]
> Все задачи, описываемые в руководстве, можно выполнять с помощью [портала Azure](sql-database-get-started-backup-recovery-portal.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Просмотр самой старой точки восстановления из резервных копий базы данных, создаваемых автоматически службой

При работе с этим разделом руководства вы просмотрите самую старую точку восстановления из [резервных копий базы данных, создаваемых автоматически службой](sql-database-automated-backups.md). 

Базу данных можно восстановить до любой точки во времени в интервале, определяемом первой точкой восстановления и последней доступной резервной копией (за шесть минут до значения текущего времени). 

В следующем фрагменте кода используется командлет [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase), чтобы получить первую точку восстановления для восстанавливаемой базы данных. Время возвращается в формате UTC, но в следующих фрагментах кода можно увидеть, как использовать местное время. Так как последняя точка восстановления активной базы данных обычно создается за шесть минут до текущего значения времени, ее легко настроить, вычтя шесть минут из соответствующего значения времени. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>Восстановление базы данных до предыдущей точки во времени

При работе с этим разделом руководства вы восстановите имеющуюся базу данных в новую базу данных до определенной точки во времени. 

>[!NOTE]
>Вы не можете изменить сервер, на который выполняется восстановление до определенной точки во времени. Чтобы выполнить восстановление на другой сервер, используйте [геовосстановление](sql-database-disaster-recovery.md#recover-using-geo-restore). Также обратите внимание, что базу данных можно восстановить в [пул эластичных баз данных](sql-database-elastic-jobs-overview.md) или другую ценовую категорию. 

В следующем фрагменте кода используется командлет [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase), чтобы восстановить базу данных $databaseToRestore, которую мы определили в предыдущем фрагменте кода. Параметр **-PointInTime** необходимо указать в формате UTC, например: *12/09/2016 20:00:00*. Фрагмент кода преобразует это значение в местное время.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и выполнить необходимые задания. Например, вы можете [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в существующую базу данных, или удалить существующую базу данных и присвоить ее имя восстановленной базе данных](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Настройка долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure 

В этом разделе вы [настроите более длительный срок хранения автоматически создаваемых резервных копий в хранилище служб восстановления Azure](sql-database-long-term-retention.md), чем определено для вашего уровня служб (до 10 лет). 


> [!TIP]
> Сведения об удалении резервных копий с длительным периодом хранения см. в статье [Настройка долгосрочного хранения создаваемых автоматически резервных копий баз данных в хранилище служб восстановления Azure с помощью PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).


### <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

> [!IMPORTANT]
> Хранилище должно находиться в том же регионе, что и логический сервер Azure SQL Server, и использовать ту же группу ресурсов.

В следующем фрагменте кода используются командлеты [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) и [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties), чтобы создать хранилище служб восстановления и задать уровень избыточности для резервных копий в хранилище. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>Настройка сервера для использования созданного хранилища служб восстановления с долгосрочным хранением резервных копий

В следующем фрагменте кода используется командлет [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault), чтобы связать созданное ранее хранилище служб восстановления с указанным сервером Azure SQL.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Создание политики хранения

Политика хранения определяет срок хранения резервной копии базы данных. 

В следующем фрагменте кода используется командлет [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject), чтобы получить политику хранения по умолчанию. Она будет использоваться как шаблон при создании других политик. Настройте в шаблоне двухлетний период хранения резервной копии и выполните командлет [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy), чтобы создать политику. 

Обратите внимание, что для некоторых командлетов нужно настроить контекст хранилища перед выполнением ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)). Поэтому вы увидите этот командлет в нескольких связанных фрагментах кода. Контекст необходимо настроить, так как политика является частью хранилища. Вы можете создать несколько политик хранения для каждого хранилища, а затем применять требуемую политику для указанных баз данных. 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Настройка базы данных для использования ранее определенной политики хранения

В следующем фрагменте используется командлет [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy), чтобы применить новую политику к указанной базе данных.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> После настройки резервные копии появятся в хранилище в течение следующих семи дней. Продолжайте работу с руководством после появления резервных копий в хранилище.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>Просмотр сведений о резервных копиях с включенной функцией долгосрочного хранения

При работе с этим разделом руководства вы просмотрите сведения о резервных копиях базы данных в хранилище с включенной функцией [долгосрочного хранения резервных копий](sql-database-long-term-retention.md). 

В следующих фрагментах кода запрашиваются сведения из хранилища с помощью командлетов [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) и [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint).

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Восстановление базы данных из резервной копии с долгосрочным хранением

При работе с этим разделом руководства вы восстановите имеющуюся базу данных из резервной копии, расположенной в хранилище служб восстановления Azure, в новую базу данных.

Как и в используемых для восстановления до точки во времени фрагментах кода (см. выше), для восстановления из резервной копии с включенной функцией долгосрочного хранения используется командлет [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase). Только в этот раз параметр **-FromPointInTimeBackup** нужно заменить параметром **-FromLongTermRetentionBackup**.

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных, или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>Полный скрипт Azure PowerShell для восстановления из предыдущей точки во времени, а также настройка и восстановление из резервной копии с включенной функцией долгосрочного хранения с помощью PowerShell

> [!NOTE]
> Если в хранилище нет резервной копии, при попытке восстановления последней резервной копии в конце этого скрипта вы получите исключение *Cannot index into a null array* (Не удается индексировать в пустой массив).

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](sql-database-automated-backups.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).

