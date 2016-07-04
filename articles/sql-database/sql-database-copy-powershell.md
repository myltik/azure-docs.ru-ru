<properties 
    pageTitle="Копирование базы данных SQL Azure с помощью PowerShell | Microsoft Azure" 
    description="Создание копии Базы данных SQL Azure с помощью PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Копирование базы данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Обзор](sql-database-copy.md)
- [Портал Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Ниже показано, как скопировать базу данных SQL с помощью PowerShell на тот же сервер или на другой сервер. Для операции копирования базы данных используется командлет [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx).


Для работы с этой статьей необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных Azure SQL. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- Azure PowerShell. Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).



## Копирование Базы данных SQL

В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и серверу. Замените значения заполнителей на значения для вашей среды:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### Копирование Базы данных SQL на тот же сервер

Эта команда отправляет в службу запрос о копировании базы данных. Операция копирования может занять некоторое время в зависимости от размера базы данных.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

### Копирование Базы данных SQL на другой сервер

Эта команда отправляет в службу запрос о копировании базы данных. Операция копирования может занять некоторое время в зависимости от размера базы данных.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Отслеживание хода выполнения операции копирования

Состояние запроса о копировании можно проверить, выполнив командлет **Start-AzureSqlDatabaseCopy**. Обычно при проверке состояния сразу же после отправки запроса возвращается результат **Состояние: ожидание** или **Состояние: выполняется**, поэтому результат **Состояние: ЗАВЕРШЕНО** может появиться не с первого раза.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName

## Разрешение имен для входа

Сведения о разрешении имен для входа после завершения операции копирования см. в разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes).


## Пример сценария PowerShell

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## Дальнейшие действия

- В разделе [Копирование Базы данных SQL Azure](sql-database-copy.md) доступны общие сведения о копировании базы данных SQL Azure.
- В разделе [Копирование базы данных SQL Azure помощью портала Azure](sql-database-copy-portal.md) рассматривается копирование базы данных с помощью портала Azure.
- В разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md) рассматривается копирование базы данных с помощью Transact-SQL.
- В разделе [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md) описывается управление пользователями и именами для входа при копировании базы данных на другой логический сервер.


## Дополнительные ресурсы

- [Управление именами для входа](sql-database-manage-logins.md)
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
- [Экспорт базы данных в BACPAC](sql-database-export.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->