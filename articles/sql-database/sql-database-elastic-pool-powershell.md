<properties 
    pageTitle="Горизонтальное масштабирование ресурсов с помощью пулов эластичных баз данных | Microsoft Azure" 
    description="Узнайте, как создать пул эластичных баз данных с помощью PowerShell для горизонтального масштабирования ресурсов и управления для нескольких баз данных." 
	keywords="несколько баз данных, горизонтальное масштабирование"    
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="02/23/2016"
    ms.author="adamkr; sstein"/>

# Создание пула эластичных баз данных с помощью PowerShell для горизонтального масштабирования ресурсов на несколько баз данных SQL 

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Узнайте, как управлять несколькими базами данных, [создав пул эластичных баз данных](sql-database-elastic-pool.md) с помощью командлетов PowerShell.

> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в один шаг [обновить его до версии 12 и создать пул](sql-database-upgrade-server-portal.md).

Пулы эластичных баз данных позволяют горизонтально масштабировать ресурсы и управление для нескольких баз данных SQL.

В этой статье мы расскажем, как подготовить все необходимое для создания и настройки пула эластичных баз данных (включая сервер версии 12), кроме подписки Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.




Для наглядности разбираются и поясняются отдельные шаги по созданию пула эластичных баз данных с помощью Azure PowerShell. Если вам нужен только краткий список команд, перейдите в раздел **Сборка** в нижней части этой статьи.


Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).




## Настройка учетных данных и выбор подписки

Теперь, после запуска модуля диспетчера ресурсов Azure, вам доступны все командлеты, необходимые для создания и настройки пула эластичных баз данных. Сначала необходимо организовать доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Login-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов. Если у вас есть подписка, запустите следующий командлет:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запускать командлеты под вашей подпиской Azure. Следующим шагом мы определим группу ресурсов для сервера, на котором будет создан пул эластичных баз данных и размещены несколько баз данных. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Чтобы получить список допустимых расположений, выполните командлет **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations**.

Если у вас уже есть группа ресурсов, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новую группу ресурсов:

	New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"

### Создание сервера 

Пулы эластичных баз данных создаются внутри серверов баз данных SQL Azure. Если у вас уже есть сервер, переходите к следующему шагу. Чтобы создать новый сервер версии 12, выполните командлет [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Замените строку ServerName на имя вашего сервера. Это имя должно быть уникальным для серверов Azure SQL Server, и, если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. После успешного создания сервера будут запрошены сведения о сервере и PowerShell. Вы можете отредактировать эту команду, чтобы использовать любое другое допустимое расположение.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.


### Настройка серверного правила брандмауэра для доступа к серверу

Установите правило брандмауэра для доступа к серверу. Выполните команду [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586.aspx), заменив начальный и конечный IP-адреса значениями, действительными для вашего компьютера.

Если ваш сервер должен предоставлять доступ к другим службам Azure, добавьте параметр **-AllowAllAzureIPs**, который добавит специальное правило брандмауэра и предоставит всему трафику Azure доступ к серверу.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Создание пула эластичных баз данных и эластичных баз данных

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу. Пул эластичных баз данных можно создать с помощью командлета [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx). Данная команда создает пул, использующий 400 единиц eDTU. Каждая база данных в пуле всегда гарантированно имеет 10 доступных единиц eDTU (DatabaseDtuMin). Отдельные базы данных в пуле могут использовать максимум 100 единиц eDTU (DatabaseDtuMax). Более подробные сведения см. в статье [Эластичные пулы Базы данных SQL Azure](sql-database-elastic-pool.md).


	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Создание эластичных баз данных или их добавление в пул эластичных баз данных

Пул, созданный на предыдущем шаге, пуст, в нем не содержится ни одной эластичной базы данных. В следующих разделах рассказывается, как создавать эластичные базы данных в пуле, а также о том, как добавлять уже существующие базы данных в пул.

*После создания пула вы сможете создавать новые эластичные базы данных в пуле и перемещать существующие базы данных в пул и из него также с помощью Transact-SQL. Подробнее об этом см. в статье [Справочник по пулу эластичных баз данных для базы данных SQL](sql-database-elastic-pool-reference.md#Transact-SQL) в разделе Transact-SQL.*

### Создание эластичной базы данных внутри пула эластичных баз данных

Чтобы создать базу данных непосредственно в пуле, выполните командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) с заданным параметром **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Перемещение существующей базы данных в пул эластичных баз данных

Чтобы переместить существующую базу данных в пул, выполните командлет [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) с заданным параметром **ElasticPoolName**.


В качестве примера создайте базу данных, которая не входит в пул эластичных баз данных.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Переместите существующую базу данных в пул эластичных баз данных.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Изменение параметров производительности пула эластичных баз данных


    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Мониторинг эластичных баз данных и пулов эластичных баз данных
Пулы эластичных баз данных формируют отчеты о метриках, которые помогут правильно масштабировать управление несколькими базами данных.


### Получение состояния операций пула эластичных баз данных

Вы можете отслеживать состояние операций пула эластичных баз данных, включая создание и обновления.

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Получение состояния перемещения эластичной базы данных в пул эластичных баз данных и из него

	Get-AzureRmSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Получение показателей потребления ресурсов для пула эластичных баз данных

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

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Извлеките дополнительные дни при повторном вызове и добавлении данных:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Отформатируйте таблицу:

    $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Получение показателей потребления ресурсов для эластичной базы данных

Эти интерфейсы API совпадают с текущими интерфейсами API (версии 12), используемыми для мониторинга использования ресурсов автономной базы данных, за исключением следующего семантического различия:

* Для этого интерфейса API полученные показатели выражаются в виде процента от каждого показателя databaseDtuMax (или эквивалентного предельного значения для базовой метрики, например ЦП, ввода/вывода и т. д.), установленного для данного пула эластичных баз данных. Например, 50 % загрузка любого из этих показателей указывает, что потребление ресурсов составляет 50 % от предельного значения БД для этого ресурса в родительском пуле эластичных баз данных. 

Получите метрики:

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

При необходимости получите дополнительные дни, повторив вызов и добавив данные:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Отформатируйте таблицу:

    $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Сборка


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Дальнейшие действия

После создания эластичного пула вы можете управлять эластичными базами данных в пуле путем создания заданий обработки эластичных БД. Эластичные задания позволяют легко и быстро выполнять сценарии T-SQL в любом количестве баз данных в пуле. Дополнительные сведения можно узнать в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md).


## Справка по эластичным базам данных

Дополнительные сведения об эластичных базах данных и их пулах, включая интерфейс API и сведения об ошибках, можно узнать в статье [Справка по эластичным пулам баз данных](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0224_2016-->