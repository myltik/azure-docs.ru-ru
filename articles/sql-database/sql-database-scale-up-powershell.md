<properties 
    pageTitle="Изменение уровня обслуживания и уровня производительности базы данных SQL Azure с помощью PowerShell" 
    description="Измените уровень производительности и уровень обслуживания базы данных SQL Azure для масштабирования базы данных с помощью PowerShell. Изменение ценовой категории базы данных SQL Azure с помощью PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/23/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL с помощью PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


В этой статье показано, как изменить уровень производительности и уровень обслуживания базы данных SQL с помощью PowerShell.

Используйте сведения, представленные в статьях [Обновление баз данных SQL уровня Web или Business до новых уровней служб](sql-database-upgrade-server-portal.md) и [Уровни служб и уровни производительности в Базе данных SQL Azure](sql-database-service-tiers.md), для определения уровня служб и уровня производительности, соответствующих Базе данных SQL Azure.

> [AZURE.IMPORTANT] Изменение уровня производительности и уровня обслуживания базы данных SQL — это оперативная операция. Это означает, что в течение всей процедуры ваша база данных будет доступна.

- При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб. 
- При обновлении базы данных с включенной [георепликацией](sql-database-geo-replication-portal) перед обновлением базы данных-источника необходимо сначала обновить базы данные-получатели до требуемого уровня производительности.
- При понижении от уровня обслуживания Premium необходимо сначала завершить все активные отношения георепликации. Чтобы остановить процесс репликации между базой данных-источником и действующими базами данных-получателями, выполните действия, описанные в разделе [Восстановление после сбоя](sql-database-disaster-recovery.md).
- Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](sql-database-business-continuity.md).
- В течение 24 часов можно выполнять не более четырех отдельных изменений базы данных (уровней служб или уровней производительности).
- Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.



**Для работы с этой статьей необходимо следующее:**

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ УЧЕТНАЯ ЗАПИСЬ**. Оформив подписку, вернитесь к этой статье.
- База данных SQL Azure. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- Azure PowerShell.


Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).



## Настройка учетных данных и выбор подписки

Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на портал Azure.

	Login-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор подписки можно скопировать из данных, которые были показаны на предыдущем этапе. Если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureSubscription** и скопируйте нужные данные из полученных результатов. Если у вас есть подписка, запустите следующий командлет:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId




## Изменение уровня обслуживания и уровня производительности базы данных SQL

Выполните командлет **Set-AzureRMSqlDatabase** и задайте в параметре **- RequestedServiceObjectiveName** уровень производительности требуемой ценовой категории, например *S0*, *S1*, *S2*, *S3*, *P1*, *P2*…

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

<!---HONumber=AcomDC_0224_2016-->