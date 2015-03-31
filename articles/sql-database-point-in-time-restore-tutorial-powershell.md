<properties 
   pageTitle="Восстановление базы данных SQL Azure с использованием восстановления на момент времени в Azure PowerShell" 
   description="Восстановление на момент времени, база данных SQL Microsoft Azure, восстановление базы данных, восстановление базы данных, Azure PowerShell" 
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
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Восстановление базы данных SQL Azure с использованием восстановления на момент времени в Azure PowerShell

> [AZURE.SELECTOR]
- [Восстановление на момент времени - портал](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [Восстановление на момент времени - API REST](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## Обзор

В этом учебнике показано, как восстановить базу данных SQL Azure с помощью восстановления на момент времени в [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). В базе данных SQL Azure есть встроенные функции резервного копирования для поддержки самостоятельного восстановления на определенный момент времени для уровней обслуживания Basic, Standard и Premium.

При восстановлении на момент времени создается новая база данных. Служба автоматически выбирает уровень обслуживания на основе резервной копии, используемой в точке восстановления. Убедитесь, что на логическом сервере есть доступная квота для создания еще одной базы данных. Если вы хотите запросить более высокую квоту, обратитесь в [службу поддержки Azure](http://azure.microsoft.com/support/options/).

## Ограничения и безопасность

См. [Восстановление базы данных SQL Azure с использованием восстановления на момент времени на портале Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

## Практическое руководство. Восстановление базы данных SQL Azure с использованием восстановления на момент времени в Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Вам необходимо использовать аутентификацию на основе сертификата для выполнения следующих командлетов. Дополнительную информацию см. в разделе *Use the certificate method* в статье [Как установить и настроить Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Получите базу данных, которую необходимо восстановить, используя командлет [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx). Укажите следующие параметры:
	* **ServerName** для имени сервера, на котором находится база данных.
	* **DatabaseName** для имени базы данных, которую хотите восстановить.	

	`PS C:\>$Database = Get-AzureSqlDatabase -ServerName "myserver" -DatabaseName "mydb"`

2. Начните восстановление, используя командлет [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Укажите следующие параметры:	
	* **SourceDatabase** для исходной базы данных, из которой требуется выполнить восстановление.
	* **TargetDatabaseName** для имени базы данных, в которую необходимо выполнить восстановление.
	* **PointInTime** для базы данных, в которую требуется выполнить восстановление.

	Сохраните результат, возвращенный переменной с именем **$RestoreRequest**. Эта переменная содержит идентификатор запроса восстановления, который используется для мониторинга состояния восстановления. 

	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database -TargetDatabaseName "myrestoredDB" -PointInTime "2015-01-01 06:00:00"`

Восстановление может занять некоторое время. Для мониторинга состояния восстановления используйте командлет [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) и укажите следующие параметры:

* **ServerName** для имени базы данных, в которую необходимо выполнить восстановление.
* **OperationGuid** для глобального уникального идентификатора, которым является идентификатор запроса восстановления, хранимый в переменной **$RestoreRequest** на шаге 2.

	`PS C:\>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

В полях **State** и **PercentComplete** отображается состояние восстановления. 

## Дальнейшие действия

Дополнительные сведения см. в следующих разделах.  

[Непрерывность бизнес-процессов базы данных SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Резервное копирование и восстановление баз данных SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Восстановление базы данных SQL Azure на определенный момент времени (блог)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
