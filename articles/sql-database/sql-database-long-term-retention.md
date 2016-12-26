---
title: "Хранение резервных копий базы данных SQL Azure до 10 лет | Документация Майкрософт"
description: "Узнайте, как база данных SQL Azure поддерживает хранение резервных копий в течение 10 лет."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 92a2cca71380ac609e900d223908eda1a40be635
ms.openlocfilehash: 17de9fd999b904b66c4e9d574fb4754069ae133a


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Хранение резервных копий базы данных SQL Azure до 10 лет
В соответствии с нормативными требованиями, стандартами соответствия или бизнес-задачами многие приложения должны хранить автоматические полные резервные копии баз данных более 7–35 дней, то есть дольше, чем при [автоматическом резервном копировании](sql-database-automated-backups.md) базы данных SQL.

Функция **долгосрочного хранения резервных копий** позволяет хранить резервные копии базы данных SQL Azure в хранилище служб восстановления Azure до 10 лет. В каждом хранилище можно разместить до 1000 баз данных. Вы можете выбрать любую резервную копию из хранилища и восстановить ее как новую базу данных.

> [!NOTE]
> В каждом хранилище в течение 24 часов можно включить до 200 баз данных. Поэтому мы рекомендуем использовать отдельное хранилище для каждого сервера, чтобы эти ограничения не мешали вам. 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>Как работает долгосрочное хранение для базы данных SQL?

Долгосрочное хранение резервных копий позволяет привязать сервер базы данных SQL Azure к хранилищу служб восстановления Azure. 

* Это хранилище должно быть создано в той же подписке Azure, что и сервер для базы данных SQL, в том же географическом регионе и в той же группе ресурсов. 
* Затем вы сможете настроить политику хранения для любой базы данных. Если эта политика настроена, полная резервная копия базы данных еженедельно копируется в хранилище служб восстановления и хранится там в течение указанного срока (до 10 лет). 
* Вы сможете восстановить любую из этих резервных копий, развернув ее в новую базу данных на любом сервере в вашей подписке. Служба хранилища Azure копирует для этой цели существующую резервную копию, и этот процесс никак не влияет на производительность существующей базы данных.

## <a name="how-do-i-enable-long-term-retention"></a>Как включить долгосрочное хранение?

Чтобы настроить долгосрочное хранение резервной копии для базы данных, выполните следующие действия.

1. Создайте хранилище служб восстановления Azure в том же регионе, той же подписке и той же группе ресурсов, где был создан сервер базы данных SQL. 
2. Зарегистрируйте сервер в хранилище.
3. Создайте политику защиты служб восстановления Azure.
4. Примените политику защиты к базе данных, для которой вам требуется долгосрочное хранение резервных копий.

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>Как восстановить базу данных, сохраненную с помощью функции долгосрочного хранения?

Для восстановления из резервной копии долгосрочного хранения сделайте следующее.

1. Получите список содержимого хранилища, в котором хранится резервная копия.
2. Получите список содержимого контейнера, который сопоставлен с нужным логическим сервером.
3. Получите список содержимого источника данных в хранилище, которое сопоставлено с нужной базой данных.
4. Получите список точек восстановления, доступных для восстановления.
5. Выполните восстановление из подходящей точки восстановления на целевой сервер в вашей подписке.

## <a name="how-much-does-long-term-retention-cost"></a>Сколько стоит долгосрочное хранение?

Плата за долгосрочное хранение для базы данных SQL Azure взимается в соответствии с [ценами на службу архивации Azure](https://azure.microsoft.com/pricing/details/backup/).

С момента регистрации сервера базы данных SQL Azure в хранилище с вас будет взиматься плата за общий объем хранилища, используемый для хранения еженедельных резервных копий.

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>Настройка долгосрочного хранения на портале Azure

В колонке сервера базы данных SQL Azure можно настроить долгосрочное хранение, а при необходимости создать хранилище служб восстановления Azure.

## <a name="configuring-long-term-retention-using-powershell"></a>Настройка долгосрочного хранения с помощью PowerShell

Чтобы настроить долгосрочное хранение с помощью PowerShell, выполните следующие шаги.
1. Создайте хранилище служб восстановления.
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. Зарегистрируйте сервер базы данных SQL Azure в хранилище, чтобы резервные копии баз данных этого сервера могли храниться в течение длительного времени.
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. Создайте политику хранения для резервных копий.
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. Включите долгосрочное хранение для базы данных SQL, резервные копии которой следует хранить в этом хранилище.
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. Получите список серверов, связанных с определенным хранилищем. Каждый сервер связан с определенным контейнером в хранилище. Список зарегистрированных серверов можно получить, выполнив следующие команды.
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. Получите список баз данных, для которых настроена политика хранения в определенном контейнере. Каждая база данных имеет связанный элемент резервного копирования в соответствующем контейнере. Имя элемента резервного копирования является производным от имени базы данных.
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>Восстановление из резервной копии долгосрочного хранения

Чтобы восстановить базу данных из резервной копии, хранящейся в хранилище службы восстановления Azure, выполните следующие действия.

1. Найдите контейнер службы восстановления, связанный с сервером базы данных SQL.
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. Найдите элемент резервного копирования, связанный с базой данных.
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. Найдите резервную копию, из которой нужно выполнить восстановление.
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. Выполните восстановление из точки восстановления в новую базу данных SQL Azure.
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>Отключение долгосрочного хранения

Служба восстановления автоматически очищает резервные копии в соответствии с настроенной политикой хранения. 

* Чтобы резервные копии определенной базы данных больше не отправлялись в хранилище, удалите политику хранения для этой базы данных.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Это не повлияет на резервные копии, которые уже находятся в хранилище. Они будут автоматически удалены службой восстановления по истечении указанного срока хранения.
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>Удаление резервных копий из хранилища служб восстановления Azure

Чтобы вручную удалить резервные копии из хранилища, выполните следующие действия.

1. Найдите нужный контейнер в хранилище для сервера myserver.
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. Определите элемент резервного копирования, который следует удалить.
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. Удалите элементы резервного копирования (все резервные копии для базы данных mydb).
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. Удалите контейнер, связанный с сервером myserver.
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>Вопросы и ответы по долгосрочному хранению

1. Вопрос. Можно ли вручную удалить из хранилища определенные резервные копии?
   Ответ. Пока нет. Хранилище будет автоматически удалять резервные копии по истечении периода хранения.
2. Вопрос. Можно ли зарегистрировать сервер в нескольких хранилищах для хранения резервных копий?
   Ответ. Пока нет. Сейчас можно сохранять резервные копии только в одном хранилище.
3. Вопрос. Можно ли использовать хранилище и сервер из разных подписок?
   Ответ. Нет. Сейчас хранилище и сервер должны находиться в одной подписке и в одной группе ресурсов.
4. Вопрос. Можно ли использовать хранилище, созданное не в том регионе, где расположен мой сервер?
   Ответ. Нет, хранилище и сервер должны находиться в одном регионе, чтобы сократить время копирования и избежать расходов на трафик.
5. Вопрос. Сколько баз данных можно хранить в одном хранилище?
   Ответ. В настоящее время поддерживаются не более 1000 баз данных на одно хранилище. 
6. В. Сколько хранилищ можно создать в одной подписке? Ответ. В одной подписке можно создать до 25 хранилищ.
7. В. Сколько баз данных можно настроить для одного хранилища в день? Ответ. Для одного хранилища можно настроить не более 200 баз данных в день.
8. Вопрос. Работает ли долгосрочное хранение с пулами эластичных баз данных?
   Ответ. Да. Политику хранения можно настроить для любой базы данных в пуле.
9. Вопрос. Можно ли выбрать время создания резервной копии?
   Ответ. Нет, расписанием резервного копирования управляет база данных SQL. Это нужно для того, чтобы минимизировать влияние на производительность баз данных.
10. Вопрос. Для моей базы данных включено прозрачное шифрование данных. Смогу ли я восстановить ее из хранилища? Ответ. Да, прозрачное шифрование данных поддерживается. Вы сможете восстановить базу данных из хранилища, даже если исходной базы данных уже не существует.
11. В. Что произойдет с резервными копиями в хранилище, если моя подписка будет приостановлена? Ответ. Если подписка приостановлена, мы храним все существующие базы данных и резервные копии, но новые резервные копии не будут сохраняться в хранилище. Когда вы восстановите подписку, служба возобновит создание резервных копий в хранилище. Хранилище станет доступным для операций восстановления с использованием тех резервных копий, которые были созданы до приостановки подписки. 
12. Вопрос. Можно ли получить доступ к файлам резервных копий базы данных SQL, чтобы скачать или восстановить их на SQL Server?
   Ответ. Пока нет.
13. Вопрос. Можно ли создать несколько расписаний (ежедневно, еженедельно, ежемесячно, ежегодно) в рамках политики хранения SQL?
   Ответ. Пока нет. Сейчас такая возможность есть только для резервных копий виртуальных машин.

## <a name="next-steps"></a>Дальнейшие действия
Резервные копии базы данных являются важной частью любой стратегии непрерывности бизнес-процессов и аварийного восстановления, так как они защищают данные от случайного повреждения или удаления. Дополнительные сведения о других решениях для базы данных SQL Azure, обеспечивающих непрерывность бизнес-процессов, см. в статье [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure](sql-database-business-continuity.md).




<!--HONumber=Nov16_HO4-->


