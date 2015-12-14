<properties 
    pageTitle="Создание копии Базы данных SQL Azure с помощью PowerShell" 
    description="Создание копии Базы данных SQL Azure с помощью PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Создание копии Базы данных SQL Azure с помощью PowerShell

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



Ниже показано, как скопировать Базу данных SQL Azure с помощью PowerShell. Операция копирования базы данных копирует Базу данных SQL в новую базу данных с помощью командлета [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx). Такая копия представляет собой моментальный снимок базы данных, созданный на том же или на другом сервере.

> [AZURE.NOTE]База данных SQL Azure автоматически создает и обслуживает резервные копии для каждой пользовательской базы данных, которую можно восстановить. Дополнительные сведения см. в статье [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md).

Скопированная база данных становится полностью работоспособной и не зависит от оригинала. На момент завершения копирования новая база данных будет транзакционно согласована с исходной базой данных. Уровень службы и производительности (ценовая категория) скопированная база данных наследует у оригинала. После завершения копирования копия становится полностью работоспособной и независимой. Именами входа, пользователями и разрешениями можно управлять независимо.


При копировании базы данных на тот же логический сервер можно использовать для обеих баз данных одинаковые имена входа. Субъект безопасности, используемый для копирования базы данных, становится владельцем новой базы данных (DBO). В копируемую базу данных копируются все пользователи, их разрешения и идентификаторы безопасности (SID).


Для работы с этой статьей необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных Azure SQL. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- Azure PowerShell. Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).



## Настройка учетных данных и выбор подписки

Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на классический портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор подписки можно скопировать из данных, которые были показаны на предыдущем этапе; если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureSubscription** и скопируйте нужные данные из полученных результатов. Если у вас есть подписка, запустите следующий командлет:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureSubscription** вы вернетесь в командную строку PowerShell. Если подписок несколько, выполните командлет **Get-AzureSubscription** и убедитесь в том, что в подписке, которую необходимо использовать, отображается **IsCurrent: True**.


## Настройка переменных для конкретной среды

В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и серверу.

Замените значения заполнителей на значения для вашей среды:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## Копирование Базы данных SQL на тот же сервер

Эта команда отправляет в службу запрос о копировании базы данных. Операция копирования может занять некоторое время в зависимости от размера базы данных.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## Копирование Базы данных SQL на другой сервер

Эта команда отправляет в службу запрос о копировании базы данных. Операция копирования может занять некоторое время в зависимости от размера базы данных.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Отслеживание хода выполнения операции копирования

Состояние запроса о копировании можно проверить, выполнив командлет **Start-AzureSqlDatabaseCopy**. Обычно при проверке состояния сразу же после отправки запроса возвращается результат **Состояние: ожидание** или **Состояние: выполняется**, поэтому результат **Состояние: ЗАВЕРШЕНО** может появиться не с первого раза.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## Копирование Базы данных SQL с помощью сценария PowerShell

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

- [Соединение с SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)
- [Экспорт базы данных в BACPAC](sql-database-export-powershell.md)


## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->