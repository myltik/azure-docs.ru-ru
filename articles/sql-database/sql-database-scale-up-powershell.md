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
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL с помощью PowerShell

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


В этой статье показано, как изменить уровень производительности и уровень обслуживания базы данных SQL с помощью PowerShell.

Используйте сведения, представленные в статьях [Обновление баз данных SQL уровня Web или Business до новых уровней служб](sql-database-upgrade-new-service-tiers.md) и [Уровни служб и уровни производительности в Базе данных SQL Azure](sql-database-service-tiers.md), для определения уровня служб и уровня производительности, соответствующих Базе данных SQL Azure.

> [AZURE.IMPORTANT]Изменение уровня производительности и уровня обслуживания базы данных SQL — это оперативная операция. Это означает, что в течение всей процедуры ваша база данных будет доступна.

- При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб. 
- При обновлении базы данных с включенной [стандартной](https://msdn.microsoft.com/library/azure/dn758204.aspx) или [активной](https://msdn.microsoft.com/library/azure/dn741339.aspx) георепликацией необходимо сначала обновить базы данные-получатели до требуемого уровня производительности перед обновлением базы данных-источника.
- При понижении от уровня обслуживания Premium необходимо сначала завершить все активные отношения георепликации. Чтобы остановить процесс репликации между базой данных-источником и действующими базами данных-получателями, выполните действия, описанные в разделе [Завершение связи непрерывной копии](https://msdn.microsoft.com/library/azure/dn741323.aspx).
- Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- В течение 24 часов можно выполнять не более четырех отдельных изменений базы данных (уровней служб или уровней производительности).
- Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.



**Для работы с этой статьей необходимо следующее:**

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных SQL Azure. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- Azure PowerShell.

> [AZURE.IMPORTANT]Начиная с выпуска предварительной версии Azure PowerShell 1.0, командлет Switch-AzureMode больше не доступен, а командлеты, которые были в модуле Azure ResourceManger, переименованы. В примерах в этой статье используется новое соглашение об именовании предварительной версии PowerShell 1.0. Дополнительные сведения см. в разделе [Устаревший командлет Switch-AzureMode в Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Чтобы выполнить командлеты PowerShell, необходимо установить и запустить Azure PowerShell. А из-за удаления Switch-AzureMode необходимо скачать и установить последнюю версию Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).



## Настройка учетных данных и выбор подписки

Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор подписки можно скопировать из данных, которые были показаны на предыдущем этапе; если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureSubscription** и скопируйте нужные данные из полученных результатов. Если у вас есть подписка, запустите следующий командлет:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

После успешного выполнения командлета **Select-AzureSubscription** вы вернетесь в командную строку PowerShell. Если подписок несколько, выполните командлет **Get-AzureSubscription** и убедитесь в том, что в подписке, которую необходимо использовать, отображается **IsCurrent: True**.


 


## Изменение уровня обслуживания и уровня производительности базы данных SQL

Выполните командлет **Set-AzureRMSqlDatabase** и задайте в параметре **- RequestedServiceObjectiveName** уровень производительности требуемой ценовой категории, например *S0*, *S1*, *S2*, *S3*, *P1*, *P2*…

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Пример скрипта PowerShell для изменения уровня обслуживания и уровня производительности базы данных SQL

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRMSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Дальнейшие действия

- [Масштабирование](sql-database-elastic-scale-get-started.md)
- [Подключение к базе данных SQL и выполнение запросов с помощью SSMS](sql-database-connect-query-ssms.md)
- [Экспорт базы данных SQL Azure](sql-database-export-powershell.md)

## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)
- [Командлеты Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/mt163521.aspx)

<!---HONumber=AcomDC_1203_2015-->