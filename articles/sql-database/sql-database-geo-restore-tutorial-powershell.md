<properties 
   pageTitle="Восстановление базы данных SQL Azure с использованием географического восстановления в Azure PowerShell" 
   description="Географическое восстановление, база данных SQL Microsoft Azure, восстановление базы данных, восстановление базы данных, Azure PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Восстановление базы данных SQL Azure с использованием географического восстановления в Azure PowerShell

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## Обзор

В этом учебнике показано, как восстановить базу данных SQL Azure с помощью географического восстановления в [Azure PowerShell](../powershell-install-configure.md). Географическое восстановление — это основной механизм защиты для аварийного восстановления баз данных SQL Azure на всех уровнях обслуживания — Basic, Standard и Premium.

## Ограничения и безопасность

См. [Восстановление базы данных SQL Azure с использованием географического восстановления на портале Azure](sql-database-geo-restore-tutorial-management-portal.md).

## Практическое руководство. Восстановление базы данных SQL Azure с использованием географического восстановления в Azure PowerShell

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell]

Вам необходимо использовать аутентификацию на основе сертификата для выполнения следующих командлетов. Дополнительную информацию см. в разделе *Использование сертификата* в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md#use-the-certificate-method).

1. Получите список баз данных, которые можно восстановить, используя командлет [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Укажите следующий параметр:
	* **ServerName** для имени сервера, на котором находится база данных.	

	`PS C:\>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. Выберите базу данных для восстановления, используя командлет [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Укажите следующие параметры:
	* **ServerName** для имени сервера, на котором находится база данных.
	* **DatabaseName** для имени базы данных, из которой необходимо выполнить восстановление.

	`PS C:\>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" –DatabaseName “mydb”`
	 
3. Начните восстановление, используя командлет [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx). Укажите следующие параметры:
	* **SourceDatabase** для исходной базы данных, которую требуется восстановить.
	* **TargetDatabaseName** для имени базы данных, в которую необходимо выполнить восстановление.
	* **TargetServerName** для имени целевого сервера, на который необходимо восстановить базу данных.

	Сохраните результат, возвращенный переменной с именем **$RestoreRequest**. Эта переменная содержит идентификатор запроса восстановления, который используется для мониторинга состояния восстановления.

	`PS C:\>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “myrecoveredDB” –TargetServerName “mytargetserver”`
	
Восстановление базы данных может занять некоторое время. Для мониторинга состояния восстановления используйте командлет [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) и укажите следующие параметры:

* **ServerName** для имени базы данных, в которую необходимо выполнить восстановление.
* **OperationGuid** для глобального уникального идентификатора, которым является идентификатор запроса восстановления, хранимый в переменной **$RecoveryRequest** на шаге 3.

	`PS C:\>Get-AzureSqlDatabaseOperation –ServerName “mytargetserver” –OperationGuid $RecoveryRequest.ID`

В полях **State** и **PercentComplete** отображается состояние восстановления.

## Дальнейшие действия

Дополнительные сведения см. в следующих разделах.

[Восстановление базы данных SQL Azure с использованием восстановления на момент времени на портале Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Обеспечение непрерывности работы базы данных SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Резервное копирование и восстановление баз данных SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Географическое восстановление баз данных SQL Azure (блог)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=August15_HO6-->