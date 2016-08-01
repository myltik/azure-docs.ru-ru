<properties 
    pageTitle="Изменение уровня обслуживания и уровня производительности базы данных SQL Azure с помощью PowerShell" 
    description="Измените уровень производительности и уровень обслуживания базы данных SQL Azure для масштабирования базы данных с помощью PowerShell. Изменение ценовой категории базы данных SQL Azure с помощью PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Уровни службы и уровни производительности описывают функции и ресурсы, доступные для базы данных SQL. Их можно обновлять в соответствии с потребностями приложения. Дополнительные сведения см. в статье [Уровни служб](sql-database-service-tiers.md).

Обратите внимание, что при изменении уровня обслуживания или уровня производительности базы данных создается реплика исходной базы данных с новым уровнем производительности, после чего устанавливаются подключения к реплике. Во время этого процесса данные не теряются, но в течение короткого периода, когда производится переподключение к реплике, соединения с базой данных становятся неактивны, поэтому некоторые текущие транзакции могут откатиться. Этот период может быть разным, но обычно он не превышает 4 секунд, а в более чем 99 % случаев его длительность составляет менее 30 секунд. В очень редких случаях, особенно если в момент отключения соединений выполняется большое количество транзакций, этот период может быть длиннее.

Длительность всего процесса вертикального масштабирования зависит от размера и уровня службы базы данных до и после изменения. Например, если база данных размером 250 ГБ переводится с уровня службы "Стандартный", на этот уровень или в его пределах, процесс должен завершиться в течение 6 часов. Если уровень производительности базы данных того же размера меняется в пределах уровня службы "Премиум", процесс должен завершиться в течение 3 часов.


- При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб.
- При обновлении базы данных с включенной [георепликацией](sql-database-geo-replication-portal.md) перед обновлением базы данных-источника необходимо сначала обновить базы данных-получатели до требуемого уровня производительности.
- При понижении от уровня обслуживания Premium необходимо сначала завершить все активные отношения георепликации. Чтобы остановить процесс репликации между базой данных-источником и активными базами данных-получателями, выполните действия, описанные в разделе [Восстановление после сбоя](sql-database-disaster-recovery.md).
- Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](sql-database-business-continuity.md).
- Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.



**Для работы с этой статьей необходимо следующее:**

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ УЧЕТНАЯ ЗАПИСЬ**. Оформив подписку, вернитесь к этой статье.
- База данных SQL Azure. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- Azure PowerShell.


[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]



## Изменение уровня обслуживания и уровня производительности базы данных SQL

Выполните командлет **Set-AzureRMSqlDatabase** и задайте в параметре **- RequestedServiceObjectiveName** уровень производительности требуемой ценовой категории, например *S0*, *S1*, *S2*, *S3*, *P1*, *P2* …

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Пример скрипта PowerShell для изменения уровня обслуживания и уровня производительности базы данных SQL

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Дальнейшие действия

- [Масштабирование](sql-database-elastic-scale-get-started.md)
- [Подключение к базе данных SQL и выполнение запросов с помощью SSMS](sql-database-connect-query-ssms.md)
- [Экспорт базы данных SQL Azure](sql-database-export-powershell.md)

## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](http://azure.microsoft.com/documentation/services/sql-database/)
- [Командлеты Базы данных SQL Azure](http://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0720_2016-->