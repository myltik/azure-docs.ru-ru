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
   ms.date="06/24/2015"
   ms.author="adamkr; sstein"/>

# Создание эластичного пула Базы данных SQL и управление им с помощью PowerShell (предварительная версия)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Обзор

В этой статье рассказывается, как создавать эластичный пул для Базы данных SQL и управлять им с помощью PowerShell.


Для наглядности отдельные шаги по созданию эластичного пула с помощью Azure PowerShell разобраны и пояснены. Если вам нужен только краткий список команд, перейдите в раздел **Сборка** в нижней части этой статьи.

В этой статье рассказывается, как подготовить все необходимое для создания и настройки эластичного пула (кроме подписки Azure). Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.

> [AZURE.NOTE]Сейчас эластичные пулы предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12.


## Предварительные требования

Для создания эластичного пула с помощью PowerShell необходимо иметь установленный и запущенный Azure PowerShell и перевести его в режим диспетчера ресурсов, чтобы получить доступ к командлетам PowerShell диспетчера ресурсов Azure.

Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).

Командлеты для создания Баз данных SQL Azure и эластичных пулов и управления ими находятся в модуле диспетчера ресурсов Azure. При запуске Azure PowerShell командлеты в модуле Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет Switch-AzureMode.

	PS C:\>Switch-AzureMode -Name AzureResourceManager

Дополнительную информацию можно узнать в статье [Использование Windows PowerShell с диспетчером ресурсов](powershell-azure-resource-manager.md).


## Настройка учетных данных и выбор подписки

Теперь, после запуска модуля диспетчера ресурсов Azure, вам доступны все командлеты, необходимые для создания и настройки эластичного пула. Сначала необходимо организовать доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	PS C:\>Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов. Если у вас есть подписка, запустите следующий командлет:

	PS C:\>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запустить командлеты под вашей подпиской Azure, и следующим шагом станет установка группы ресурсов, содержащей сервер, на котором будет создаваться эластичный пул. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Выполните команду **(Get-AzureLocation | where-object {$_.Name -eq "Microsoft.Sql/servers" }).Locations** для получения списка допустимых расположений.

Если у вас уже есть группа ресурсов, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новую группу ресурсов:

	PS C:\>New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Создание сервера 

Эластичные пулы создаются внутри серверов баз данных SQL Azure. Если у вас уже есть сервер, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новый сервер версии 12. Замените строку ServerName на имя вашего сервера. Это имя должно быть уникальным для серверов SQL Azure, поэтому, если оно уже занято, может появиться ошибка. Кроме того, выполнение этой команды может занять несколько минут. После успешного создания сервера будут запрошены сведения о сервере и PowerShell. Вы можете отредактировать эту команду, чтобы использовать любое другое допустимое расположение.

	PS C:\>New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.


### Настройка серверного правила брандмауэра для доступа к серверу

Установите правило брандмауэра для доступа к серверу. Выполните следующую команду, заменив начальный и конечный IP-адреса значениями, допустимыми для вашего компьютера.

Если ваш сервер должен предоставлять доступ к другим службам Azure, добавьте параметр **-AllowAllAzureIPs**, который добавит специальное правило брандмауэра и предоставит всему трафику Azure доступ к серверу.

	PS C:\>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Создание эластичного пула и эластичных баз данных

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу. Следующая команда создаст эластичный пул. Данная команда создает пул, использующий 400 единиц передачи данных (DTU). Каждая база данных в пуле всегда гарантированно имеет 10 доступных единиц передачи данных (DatabaseDtuMin). Отдельные базы данных в пуле могут использовать максимум 100 единиц передачи данных (DatabaseDtuMax). Более подробные сведения см. в статье [Эластичные пулы Базы данных SQL Azure](sql-database-elastic-pool.md).


	PS C:\>New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Создание или добавление эластичных баз данных в пул

Эластичный пул, созданный на предыдущем шаге, пуст, в нем не содержится ни одной базы данных. В следующих разделах рассказывается, как создавать новые базы данных в эластичном пуле, а также как добавлять уже существующие базы данных в пул.


### Создание эластичной базы данных внутри эластичного пула

Для создания новой базы данных прямо в эластичном пуле воспользуйтесь командлетом **New-AzureSqlDatabase** и задайте параметр **ElasticPoolName**.


	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Перемещение существующей базы данных в эластичный пул

Для перемещения существующей базы данных в эластичный пул воспользуйтесь командлетом **Set-AzurSqlDatabase** и установите параметр **ElasticPoolName**.


В качестве примера создайте базу данных, не находящуюся в эластичном пуле.

	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Переместите существующую базу данных в эластичный пул.

	PS C:\>Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Мониторинг эластичных баз данных и эластичных пулов

### Получение состояния операций эластичного пула

Вы можете отслеживать состояние операций эластичного пула, включая создание и обновления.

	PS C:\> Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Получение состояния перемещения эластичной базы данных в эластичный пул и из него

	PS C:\>Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Получение показателей потребления ресурсов для эластичного пула

Показатели, которые могут быть возвращены в виде процента от предельного значения пула ресурсов:

* Средняя загрузка ЦП — cpu_percent 
* Средняя загрузка ввода/вывода — data_io_percent 
* Средняя загрузка журнала — log_write_percent 
* Средняя загрузка памяти — memory_percent 
* Среднее использование DTU (как максимальное значение загрузки ЦП/ввода и вывода/журнала) — DTU_percent 
* Максимальное количество одновременных пользовательских запросов (работников) — max_concurrent_requests 
* Максимальное количество одновременных пользовательских сеансов — max_concurrent_sessions 
* Общий размер хранилища для эластичного пула — storage_in_megabytes 


Детализация показателей и сроки хранения:

* Детализация данных с точностью до 5 минут.  
* Срок хранения данных — 14 дней.  


Этот командлет и интерфейс API ограничивают количество строк, которые могут быть получены за один вызов, до 1000 строк (данные приблизительно за 3 дня при детализации с точностью до 5 минут). Однако эта команда может быть вызвана несколько раз с различными начальным и конечным интервалами, чтобы получить дополнительные данные.


Получите показатели:

	PS C:\> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Извлеките дополнительные дни при повторном вызове и добавлении данных:

	PS C:\> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Отформатируйте таблицу:

    PS C:\> $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    PS C:\> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Получение показателей потребления ресурсов для эластичной базы данных

Эти интерфейсы API совпадают с текущими интерфейсами API (версии 12), используемыми для мониторинга использования ресурсов автономной базы данных, за исключением следующего семантического различия:

* Для этого интерфейса API полученные показатели выражаются в виде процента от каждого показателя databaseDtuMax (или эквивалентного предельного значения для базовой метрики, например ЦП, ввода/вывода и т. д.), установленного для данного эластичного пула. Например, 50 % загрузка любого из этих показателей указывает, что потребление ресурсов составляет 50 % от предельного значения БД для этого ресурса в родительском эластичном пуле. 

Получите метрики: PS C:\> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

При необходимости получите дополнительные дни, повторив вызов и добавив данные:

    PS C:\> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Отформатируйте таблицу:

    PS C:\> $table = Format-MetricsAsTable $metrics 

Экспортируйте в CSV-файл:

    PS C:\> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


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

После создания эластичного пула вы можете управлять его эластичными базами данных путем создания эластичных заданий. Эластичные задания позволяют легко и быстро выполнять сценарии T-SQL в любом количестве баз данных в пуле.

Дополнительные сведения можно узнать в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md).


## Справка по эластичным базам данных

Дополнительные сведения об эластичных базах данных и их пулах, включая интерфейс API и сведения об ошибках, можно узнать в статье [Справка по эластичным пулам баз данных](sql-database-elastic-pool-reference.md).

<!---HONumber=July15_HO4-->