---
title: "PowerShell: создание эластичного пула SQL Azure и управление им | Документация Майкрософт"
description: "Узнайте, как управлять пулом эластичных баз данных с помощью PowerShell."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 637171b775d01e16cec1a7e9ef6fad73875eac69
ms.openlocfilehash: e04a802b662954ff22cf44e144982dbf0964da8e


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>Создание эластичного пула и управление им с помощью PowerShell
В этой статье показано, как с помощью PowerShell создавать масштабируемые [эластичные пулы](sql-database-elastic-pool.md) и управлять ими.  Вы также можете создать эластичный пул Azure и управлять им на [портале Azure](https://portal.azure.com/), с помощью REST API или [C#] (см. статью [Мониторинг пула эластичных баз данных и управление им с помощью C#](sql-database-elastic-pool-manage-csharp.md)). Вы также можете использовать [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), чтобы создавать новые базы данных в эластичных пулах и перемещать базы данных в пулы и обратно.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>Создание эластичного пула
Эластичный пул создается с помощью командлета [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx). Значения eDTU на пул, а также минимальные и максимальные значения DTU ограничены значением уровня службы ("Базовый", "Стандартный" или "Премиум"). См. раздел [eDTU и размеры хранилища для пулов эластичных БД](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Создание базы данных внутри эластичного пула
Используйте командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) и укажите целевой пул в качестве параметра **ElasticPoolName**. Сведения о перемещении существующей базы данных в эластичный пул см. в разделе [Перемещение базы данных в пул эластичных БД](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### <a name="complete-script"></a>Полный скрипт
Этот скрипт создает группу ресурсов Azure и сервер. При появлении запроса укажите имя и пароль пользователя-администратора для нового сервера (не свои учетные данные Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>Создание эластичного пула и добавление нескольких баз данных в пул
Создание большого количества баз данных в эластичном пуле может занять некоторое время, если эта операция выполняется с помощью портала или командлетов PowerShell, которые создают базы данных поочередно. Сведения об автоматическом создании баз данных в эластичном пуле см. в документе [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="move-a-database-into-an-elastic-pool"></a>Перемещение базы данных в пул эластичных БД
Переместить базу данных в эластичный пул или из него можно с помощью командлета [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx).

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-an-elastic-pool"></a>Изменение параметров производительности эластичного пула
Если производительность недостаточна, можно изменить параметры пула в соответствии с ростом нагрузки. Используйте командлет [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511\(v=azure.300\).aspx). Присвойте параметру -Dtu значение eDTU, выделяемых на пул. Возможные значения приведены в разделе [eDTU и размеры хранилища для эластичных баз данных и пулов эластичных баз данных](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).  

    Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50

## <a name="get-the-status-of-pool-operations"></a>Получение состояния операций пула
Создание эластичного пула может занять некоторое время. Отслеживать состояние операций пула, включая создание и обновление, можно с помощью командлета [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx).

    Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>Получение состояния перемещения базы данных в эластичный пул и из него
На перемещение базы данных может потребоваться время. Отслеживать состояние перемещения можно с помощью командлета [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Получение данных об использовании ресурсов в эластичном пуле
Показатели, которые могут быть возвращены в виде процента от предельного значения пула ресурсов:   

| Имя метрики | Описание |
|:--- |:--- |
| cpu\_percent |Среднее использование вычислительных ресурсов в процентах от предела пула. |
| physical\_data\_read\_percent |Среднее использование ввода-вывода в процентах от предела пула. |
| log\_write\_percent |Среднее использование записи ресурсов в процентах от предела пула. |
| DTU\_consumption\_percent |Среднее использование eDTU в процентах от предела пула. |
| storage\_percent |Среднее использование хранилища в процентах от предела пула. |
| workers\_percent |Максимальное число одновременных рабочих ролей (запросов) в процентах от предела пула. |
| sessions\_percent |Максимальное число одновременных сеансов в процентах от предела пула. |
| eDTU_limit |Текущее максимальное значение параметра DTU для этого пула эластичных БД в течение этого интервала. |
| storage\_limit |Текущее максимальное значение размера хранилища в мегабайтах для этого пула эластичных БД в течение этого интервала. |
| eDTU\_used |Среднее число eDTU, использованных пулом на этом интервале. |
| storage\_used |Средняя емкость хранилища, использованная пулом на этом интервале. |

**Детализация показателей и сроки хранения:**

* Детализация данных с точностью до 5 минут.  
* Срок хранения данных — 35 дней.  

Этот командлет и API-интерфейс ограничивают количество строк, которые могут быть получены за один вызов, до 1000 строк (данные приблизительно за 3 дня при детализации с точностью до 5 минут). Однако эта команда может быть вызвана несколько раз с различными начальным и конечным интервалами, чтобы получить дополнительные данные.

Вот как можно получить метрики.

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>Получение данных об использовании ресурсов для базы данных в эластичном пуле
Эти API-интерфейсы совпадают с текущими API-интерфейсами (V12), используемыми для мониторинга использования ресурсов отдельной базой данных, за исключением следующего семантического различия: полученные метрики выражаются в процентах от максимального количества eDTU для каждой базы данных (или в виде эквивалентного предельного значения для базовой метрики, такой как производительность ЦП или операции ввода-вывода), установленного для данного пула. Например, загрузка в 50 % для любого из этих показателей указывает, что потребление данного ресурса составляет 50 % от предельного значения потребления этого ресурса на одну базу данных для этого ресурса в родительском пуле.

Вот как можно получить метрики.

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Добавление оповещения для ресурса эластичного пула
Для эластичного пула можно добавить правила генерации оповещений, чтобы отправлять по электронной почте уведомления или строки оповещения в [конечные точки URL-адресов](https://msdn.microsoft.com/library/mt718036.aspx), когда эластичный пул достигает заданного порогового значения использования. Используйте командлет Add-AzureRmMetricAlertRule.

> [!IMPORTANT]
> Для данных мониторинга использования ресурсов в эластичных пулах задержка как минимум 20 минут является нормальной. Настройка интервала менее 30 минут для оповещений об эластичных пулах сейчас не поддерживается. Оповещения, заданные для эластичных пулов, с интервалом (параметр -WindowSize в API PowerShell) менее 30 минут, могут не активироваться. Проследите, чтобы во всех оповещениях, определяемых для эластичных пулов, использовался интервал (WindowSize) не менее 30 минут.
>
>

Этот пример добавляет оповещение, уведомляющее, когда потребление eDTU в эластичном пуле выходит за пределы определенного порогового значения.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>Добавление оповещений для всех баз данных в эластичном пуле
Для всех баз данных в пуле эластичных БД можно добавить правила генерации оповещений, чтобы отправлять по электронной почте уведомления или строки оповещения в [конечные точки URL-адресов](https://msdn.microsoft.com/library/mt718036.aspx) , когда ресурс достигает порогового значения использования, определенного в оповещении.

> [!IMPORTANT]
> Для данных мониторинга использования ресурсов в эластичных пулах задержка как минимум 20 минут является нормальной. Настройка интервала менее 30 минут для оповещений об эластичных пулах сейчас не поддерживается. Оповещения, заданные для эластичных пулов, с интервалом (параметр -WindowSize в API PowerShell) менее 30 минут, могут не активироваться. Проследите, чтобы во всех оповещениях, определяемых для эластичных пулов, использовался интервал (WindowSize) не менее 30 минут.
>
>

Этот пример добавляет оповещение для каждой базы данных в эластичном пуле, которое уведомляет, когда потребление DTU в этой базе данных превышает определенное пороговое значение.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    }

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Сбор и отслеживание данных об использовании ресурсов для нескольких пулов в подписке
Если в вашей подписке много баз данных, отслеживать отдельно каждый эластичный пул становится затруднительно. Вместо этого с помощью командлетов PowerShell базы данных SQL и запросов T-SQL можно собирать данные об использовании ресурсов по нескольким пулам и базам данных. Это упрощает мониторинг и анализ использования ресурсов. В репозитории примеров для SQL Server на сайте GitHub вы найдете [пример реализации](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) набора сценариев PowerShell вместе с документацией по назначению и методам использования этого набора.

Чтобы использовать этот пример реализации, выполните следующие действия.

1. Скачайте [сценарии и документацию](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools).
2. Измените скрипты, указав параметры своей среды. Укажите один или несколько серверов, на которых размещаются пулы эластичных баз данных.
3. Укажите базу данных телеметрии, где будут храниться собранные метрики.
4. Укажите лимит времени на выполнение скриптов.

Основные действия, которые выполняют эти скрипты:

* перечисляют все серверы в указанной подписке Azure (или в указанном списке серверов);
* запускают фоновое задание для каждого сервера. Задание выполняется в цикле через регулярные интервалы и собирает данные телеметрии для всех пулов на сервере. Затем собранные данные загружаются в указанную базу данных телеметрии;
* перечисляют список баз данных в каждом пуле, чтобы собрать данные об использовании ресурсов базы данных. Затем собранные данные загружаются в базу данных телеметрии.

Собранные в базе данных телеметрии метрики можно анализировать для контроля состояния пулов эластичных баз данных и входящих в них баз данных. Также скрипт определяет в базе данных телеметрии функцию, возвращающую табличное значение, которая помогает обрабатывать значения метрик за указанный интервал времени. Например, с помощью этой функции можно отобразить N эластичных пулов с максимальной загрузкой eDTU за заданный период. Также вы можете использовать для получения и анализа собранных данных любой аналитический инструмент, например Excel или Power BI.

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>Пример. Получение метрик потребления ресурсов в эластичном пуле и его базах данных
В этом примере извлекаются метрики потребления для данного пула эластичных БД и всех его баз данных. Собранные данные форматируются и записываются в CSV-файл. Этот файл можно просмотреть в Excel.

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }

    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) {
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name;
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId
          }
          $table = $table += $sx
      }
    }

    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }

    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv

## <a name="latency-of-elastic-pool-operations"></a>Задержка операций эластичного пула
* Изменение минимального или максимального числа eDTU для базы данных обычно завершается за 5 минут.
* Изменение числа eDTU на пул зависит от общей емкости, используемой всеми базами данных в пуле. Изменение занимает порядка 90 минут или меньше на каждые 100 ГБ. Например, если общее пространство, используемое всеми базами данных в пуле, равно 200 ГБ, то ожидаемая задержка при изменении числа eDTU для пула составит до 3 часов.

Справочную документацию по этим командлетам PowerShell см. в таких разделах:

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582\(v=azure.300\).aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403\(v=azure.300\).aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589\(v=azure.300\).aspx)

Командлет Stop- означает отмену, а не приостановку. После его выполнения возобновить обновление невозможно, можно только запустить его сначала. Командлет Stop- очищает и высвобождает все соответствующие ресурсы.

## <a name="next-steps"></a>Дальнейшие действия
* [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md). Задания обработки для эластичных баз данных упрощают выполнение сценариев T-SQL для любого количества баз данных в пуле.
* См. статью [Развертывание с помощью базы данных SQL Azure](sql-database-elastic-scale-introduction.md). В ней описывается использование инструментов эластичной базы данных для развертывания, перемещения данных, выполнения запросов и создания транзакций.



<!--HONumber=Feb17_HO2-->


