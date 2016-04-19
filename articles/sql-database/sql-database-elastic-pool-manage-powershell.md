<properties 
    pageTitle="Управление пулом эластичных баз данных (PowerShell) | Microsoft Azure" 
    description="Узнайте, как управлять пулом эластичных баз данных с помощью PowerShell."  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/01/2016"
    ms.author="sstein"/>

# Мониторинг, управление и выбор размера пула эластичных баз данных с помощью PowerShell 

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Узнайте, как управлять [пулом эластичных баз данных](sql-database-elastic-pool.md) с помощью командлетов PowerShell.

Стандартные коды ошибок см. в статье [Коды ошибок SQL для клиентских приложений базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в один шаг [обновить его до версии 12 и создать пул](sql-database-upgrade-server-portal.md).

Для работы вам понадобится Azure PowerShell 1.0 или более поздняя версия. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).




## Создание эластичной базы данных в пуле

Чтобы создать базу данных непосредственно в пуле, выполните командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) с заданным параметром **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Перемещение автономной базы данных в пул

Чтобы переместить существующую базу данных в пул, выполните командлет [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) с заданным параметром **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Изменение параметров производительности пула

Чтобы изменить параметры производительности пула, используйте командлет [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx).

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Получение состояния операций пула

Вы можете отслеживать состояние операций пула, включая создание и обновление, с помощью командлета [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Получение состояния перемещения эластичной базы данных в пул и из него

Вы можете отслеживать состояние операций эластичных баз данных, включая создание и обновления, с помощью командлета [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Получение данных по использованию пула

Показатели, которые могут быть возвращены в виде процента от предельного значения пула ресурсов:

* Средняя загрузка ЦП — cpu\_percent 
* Средняя загрузка ввода/вывода — data\_io\_percent 
* Средняя загрузка журнала — log\_write\_percent 
* Средняя загрузка памяти — memory\_percent 
* Среднее использование eDTU (как максимальное значение загрузки ЦП/ввода и вывода/журнала) — DTU\_percent 
* Максимальное количество одновременных пользовательских запросов (работников) — max\_concurrent\_requests 
* Максимальное количество одновременных пользовательских сеансов — max\_concurrent\_sessions 
* Общий размер хранилища для эластичного пула — storage\_in\_megabytes 


Детализация показателей и сроки хранения:

* Детализация данных с точностью до 5 минут.  
* Срок хранения данных — 14 дней.  


Этот командлет и интерфейс API ограничивают количество строк, которые могут быть получены за один вызов, до 1000 строк (данные приблизительно за 3 дня при детализации с точностью до 5 минут). Однако эта команда может быть вызвана несколько раз с различными начальным и конечным интервалами, чтобы получить дополнительные данные.


Получите показатели:

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Извлеките дополнительные дни при повторном вызове и добавлении данных:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Отформатируйте таблицу:

    $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## Получение показателей потребления ресурсов для эластичной базы данных

Эти интерфейсы API совпадают с текущими интерфейсами API (версии 12), используемыми для мониторинга использования ресурсов автономной базы данных, за исключением следующего семантического различия:

* Для этого интерфейса API полученные показатели выражаются в виде процента от каждого показателя databaseDtuMax (или эквивалентного предельного значения для базовой метрики, например ЦП, ввод/вывод и т. д.), установленного для данного пула. Например, загрузка в 50 % для любого из этих показателей указывает, что потребление данного ресурса составляет 50 % от предельного значения потребления этого ресурса на одну БД для этого ресурса в родительском пуле. 

Получите метрики:

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

При необходимости получите дополнительные дни, повторив вызов и добавив данные:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Отформатируйте таблицу:

    $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Пример мониторинга пула и управления им с помощью PowerShell


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Дальнейшие действия

- [Создание заданий обработки эластичных БД](sql-database-elastic-jobs-overview.md). Эти задания упрощают выполнение сценариев T-SQL для любого количества эластичных баз данных в пуле.


## Справка по эластичным базам данных

Дополнительные сведения об эластичных базах данных и их пулах, включая интерфейс API и сведения об ошибках, можно узнать в статье [Справка по эластичным пулам баз данных](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0406_2016-->