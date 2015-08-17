<properties 
   pageTitle="Создание эластичного пула баз данных для Базы данных SQL и управление им с помощью PowerShell" 
   description="Создание эластичного пула баз данных для Базы данных SQL Azure и управление им с помощью PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="07/28/2015"
   ms.author="adamkr; sstein"/>

# Создание пула эластичных баз данных для Базы данных SQL и управление им с помощью PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Обзор

Эта статья рассказывает о создании пула эластичных баз данных SQL и управлении им с помощью PowerShell.


Для наглядности разбираются и поясняются отдельные шаги по созданию пула эластичных баз данных с помощью Azure PowerShell. Если вам нужен только краткий список команд, перейдите в раздел **Сборка** в нижней части этой статьи.

В этой статье рассказывается, как подготовить все необходимое для создания и настройки пула эластичных баз данных (кроме подписки Azure). Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.

> [AZURE.NOTE]Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами баз данных SQL версии 12.


## Предварительные требования

Для создания пула эластичных баз данных с помощью PowerShell необходимо иметь установленный и запущенный Azure PowerShell и перевести его в режим диспетчера ресурсов, чтобы получить доступ к командлетам PowerShell диспетчера ресурсов Azure.

Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).

Командлеты для создания баз данных SQL и пулов эластичных баз данных находятся в модуле диспетчера ресурсов Azure. При запуске Azure PowerShell командлеты в модуле Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Дополнительную информацию можно узнать в статье [Использование Windows PowerShell с диспетчером ресурсов](powershell-azure-resource-manager.md).


## Настройка учетных данных и выбор подписки

Теперь, после запуска модуля диспетчера ресурсов Azure, вам доступны все командлеты, необходимые для создания и настройки пула. Сначала необходимо организовать доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов. Если у вас есть подписка, запустите следующий командлет:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запускать командлеты в своей подписке Azure. Поэтому следующим шагом станет установка группы ресурсов, содержащей сервер, на котором будет создаваться пул. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Выполните команду **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** для получения списка допустимых расположений.

Если у вас уже есть группа ресурсов, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новую группу ресурсов:

	New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Создание сервера 

Пулы эластичных баз данных создаются внутри серверов баз данных SQL Azure. Если у вас уже есть сервер, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новый сервер версии 12. Замените строку ServerName на имя вашего сервера. Это имя должно быть уникальным для серверов SQL Azure, и, если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. После успешного создания сервера будут запрошены сведения о сервере и PowerShell. Вы можете отредактировать эту команду, чтобы использовать любое другое допустимое расположение.

	New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.


### Настройка серверного правила брандмауэра для доступа к серверу

Установите правило брандмауэра для доступа к серверу. Выполните следующую команду, заменив начальный и конечный IP-адреса значениями, допустимыми для вашего компьютера.

Если ваш сервер должен предоставлять доступ к другим службам Azure, добавьте параметр **-AllowAllAzureIPs**, который добавит специальное правило брандмауэра и предоставит всему трафику Azure доступ к серверу.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Создание пула эластичных баз данных и эластичных баз данных

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу. Следующая команда создаст пул. Данная команда создает пул, использующий 400 единиц передачи данных (DTU). Каждая база данных в пуле всегда гарантированно имеет 10 доступных единиц передачи данных (DatabaseDtuMin). Отдельные базы данных в пуле могут использовать максимум 100 единиц передачи данных (DatabaseDtuMax). Более подробные сведения см. в статье [Эластичные пулы Базы данных SQL Azure](sql-database-elastic-pool.md).


	New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Создание или добавление эластичных баз данных в пул

Пул, созданный на предыдущем шаге, пуст. В нем не содержится ни одной базы данных. В следующих разделах рассказывается о том, как создавать новые базы данных в пуле и как добавлять в пул существующие базы данных.


### Создание эластичной базы данных внутри пула эластичных баз данных

Для создания новой базы данных прямо в пуле воспользуйтесь командлетом **New-AzureSqlDatabase** и задайте параметр **ElasticPoolName**.


	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Перемещение существующей базы данных в пул эластичных баз данных

Для перемещения существующей базы данных в пул воспользуйтесь командлетом **Set-AzurSqlDatabase** и задайте параметр **ElasticPoolName**.


В качестве примера создайте базу данных, которая не входит в пул эластичных баз данных.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Переместите существующую базу данных в пул.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Мониторинг эластичных баз данных и пулов эластичных баз данных

### Получение состояния операций пула эластичных баз данных

Вы можете отслеживать состояние операций пула, включая создание и обновление.

	Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Получение состояния перемещения эластичной базы данных в пул эластичных баз данных и из него

	Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Получение показателей потребления ресурсов для пула эластичных баз данных

Показатели потребления ресурсов выражаются в процентах от предельных значений для пула.

* Средняя загрузка ЦП — cpu\_percent 
* Средняя загрузка ввода/вывода — data\_io\_percent 
* Средняя загрузка журнала — log\_write\_percent 
* Средняя загрузка памяти — memory\_percent 
* Среднее использование DTU (как максимальное значение загрузки ЦП/ввода и вывода/журнала) — DTU\_percent 
* Максимальное количество одновременных пользовательских запросов (работников) — max\_concurrent\_requests 
* Максимальное количество одновременных пользовательских сеансов — max\_concurrent\_sessions 
* Общий размер хранилища пула — storage\_in\_megabytes 


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

* Для этого интерфейса API полученные показатели выражаются в виде процента от каждого показателя databaseDtuMax (или эквивалентного предельного значения для базовой метрики, например ЦП, ввод/вывод и т. д.), установленного для данного пула. Например, загрузка в 50 % для любого из этих показателей указывает, что потребление данного ресурса составляет 50 % от предельного значения потребления этого ресурса на одну БД для этого ресурса в родительском пуле. 

Получение метрики: $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

При необходимости получите дополнительные дни, повторив вызов и добавив данные:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Отформатируйте таблицу:

    $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Сборка


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Дальнейшие действия

После создания эластичного пула вы можете управлять эластичными базами данных в пуле путем создания заданий обработки эластичных БД. Задания обработки эластичных БД позволяют легко выполнять сценарии T-SQL для любого количества эластичных баз данных в пуле.

Дополнительные сведения можно узнать в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md).


## Справка по эластичным базам данных

Дополнительные сведения об эластичных базах данных и их пулах, включая интерфейс API и сведения об ошибках, можно узнать в статье [Справка по эластичным пулам баз данных](sql-database-elastic-pool-reference.md).

<!---HONumber=August15_HO6-->