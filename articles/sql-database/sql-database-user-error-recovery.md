<properties 
   pageTitle="Восстановление Базы данных SQL после ошибок пользователей" 
   description="Вы узнаете, как выполнить восстановление базы данных после ошибки пользователя, случайного повреждения данных или удаления с помощью функции восстановления на момент времени (PITR) Базы данных SQL Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/09/2015"
   ms.author="elfish"/>

# Восстановление базы данных SQL Azure после ошибки пользователя

База данных SQL Azure предоставляет два основных способа восстановления после ошибки пользователя или непреднамеренного изменения данных.

- Восстановление до точки во времени 
- Восстановление удаленной базы данных

Подробнее о данных возможностях вы можете узнать из этой [записи блога](https://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/).

База данных SQL Azure всегда восстанавливается в новую базу данных. Эти способы восстановления доступны для всех баз данных уровней Basic, Standard и Premium.

##Восстановление до точки во времени
В случае ошибки пользователя или непреднамеренного изменения данных можно воспользоваться восстановлением до точки во времени, чтобы восстановить базу данных на любой момент времени в пределах срока хранения.

Срок хранения баз данных уровня Basic составляет 7 дней, Standard — 14 дней, а Premium — 35 дней. Дополнительные сведения о хранении баз данных см. в статье [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md).

> [AZURE.NOTE] При восстановлении базы данных создается новая база данных. Важно убедиться, что на целевом сервере восстановления есть достаточный объем DTU для новой базы данных. Можно [обратиться в службу поддержки](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) с просьбой увеличить квоту.

###Портал Azure
Чтобы воспользоваться функцией восстановления системы до точки во времени на портале Azure, выполните указанные ниже действия.

1. Войдите на [портал Azure](https://portal.Azure.com).
2. В левой части экрана выберите **ОБЗОР**, а затем — **Базы данных SQL**.
3. Перейдите к нужной базе данных и выберите ее.
4. В верхней части колонки своей базы данных выберите **Восстановить**.
5. Укажите имя базы данных и момент времени, после чего нажмите **Создать**.
6. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **УВЕДОМЛЕНИЙ** в левой части экрана.

###PowerShell
PowerShell позволяет восстановить состояние системы до точки во времени программными средствами с помощью командлета [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Для получения подробной информации по решению этой задачи [посмотрите эту видеоинструкцию](https://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/).

> [AZURE.IMPORTANT] Эта статья содержит команды для Azure PowerShell версии вплоть до 1.0, *но не включая* саму версию 1.0 и более поздние. Используемую версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**.

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###Интерфейс REST API 
Используйте REST для программного восстановления базы данных.

1. Получите базу данных, которую необходимо восстановить, используя операцию [Get Database](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Создайте запрос на восстановление, используя операцию [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Отследите запрос на восстановление, используя операцию [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

##Восстановление удаленной базы данных.
Если база данных удалена, База данных SQL Azure позволяет восстановить ее на момент удаления. База данных SQL Azure хранит резервную копию удаленной базы данных в течение срока хранения.

Срок хранения удаленной базы данных определяется в зависимости от уровня службы базы данных во время ее существования или от количества дней, в течение которых существовала база данных. Выбирается меньшее значение. Подробнее о хранении баз данных можно прочесть в статье [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md).

> [AZURE.NOTE] При восстановлении базы данных создается новая база данных. Важно убедиться, что на целевом сервере восстановления есть достаточный объем DTU для новой базы данных. Можно [обратиться в службу поддержки](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) с просьбой увеличить квоту.

###Портал Azure
Чтобы восстановить удаленную базу данных SQL на портале Azure, выполните указанные ниже действия.

1. Войдите на [портал Azure](https://portal.Azure.com).
2. В левой части экрана выберите **ОБЗОР**, а затем — **Серверы SQL**.
3. Перейдите к нужному серверу и выберите ее.
4. Прокрутите экран вниз до раздела **Операции** в колонке сервера и щелкните плитку **Удаленные базы данных**.
5. Выберите удаленную базу данных, которую хотите восстановить.
6. Укажите имя базы данных и нажмите **Создать**.
7. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **УВЕДОМЛЕНИЙ** в левой части экрана.

###PowerShell
Чтобы восстановить удаленную базу данных с помощью PowerShell, используйте командлет [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Для получения подробной информации по решению этой задачи [посмотрите эту видеоинструкцию](https://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/).

1. Найдите удаленную базу данных и дату ее удаления в списке удаленных баз данных.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. Получите конкретную удаленную базу данных и начинайте восстановление.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###Интерфейс REST API 
Используйте REST для программного восстановления базы данных.

1.	Создайте список всех своих удаленных баз данных, которые можно восстановить, используя операцию [List Restorable Dropped Databases](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Получите подробную информацию об удаленной базе данных, которую необходимо восстановить, используя операцию [Get Restorable Dropped Database](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Начните восстановление, используя операцию [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Отследите состояние восстановления, используя операцию [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

<!---HONumber=AcomDC_0128_2016-->