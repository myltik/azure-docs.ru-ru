<properties
	pageTitle="Обновление базы данных SQL Azure до версии 12 с помощью PowerShell | Microsoft Azure"
	description="Объясняется, как выполнить обновление базы данных SQL Azure до версии 12, включая способы обновления баз данных Web и Business, а также как выполнить обновление сервера версии 11 путем переноса баз данных непосредственно в пул эластичных баз данных с помощью PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="sstein"/>

# Обновление базы данных SQL Azure до версии 12 с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


Версия 12 базы данных SQL является последней версией, поэтому рекомендуется выполнить обновление до нее. У базы данных SQL версии 12 есть множество [преимуществ по сравнению с предыдущей версией](sql-database-v12-whats-new.md), включая следующие:

- Улучшенная совместимость с сервером SQL Server.
- Улучшенная производительность для категории «Премиум» и новые уровни производительности.
- [Пулы эластичных баз данных](sql-database-elastic-pool.md).

Эта статья содержит инструкции для обновления серверов баз данных SQL версии 11 до версии 12.

В процессе обновления до версии 12 выполняется обновление всех баз данных Web и Business до нового уровня служб, поэтому в статью также включены инструкции по обновлению баз данных Web и Business.

Кроме того, переход на [пул эластичных баз данных](sql-database-elastic-pool.md) может быть более экономически эффективным, чем обновление до индивидуальных уровней производительности (ценовых категорий) для отдельных баз данных. Пулы также упрощают управление базами данных, поскольку с ними необходимо управлять только параметрами производительности для пула, а не уровнями производительности отдельных баз данных. Если ваши базы данных расположены на нескольких серверах, подумайте об их переносе на один и тот же сервер и помещении в пул.

Вы можете легко перенести базы данных с серверов версии 11 непосредственно в пулы эластичных баз данных, выполнив действия, описанные в этой статье.

Обратите внимание, что на протяжении всей операции обновления базы данных останутся в сети и будут продолжать работать. Во время фактического перехода на новый уровень производительности может произойти кратковременное отключение от базы данных. Обычно оно составляет порядка 90 секунд, но может длиться до 5 минут. Если в приложении предусмотрена [обработка временных отказов подключения](sql-database-connectivity-issues.md), то этого достаточно, чтобы обеспечить защиту от отключения на завершающем этапе обновления.

Обновление базы данных SQL до версии 12 не может быть отменено. После обновления сервер нельзя вернуть к версии 11.

После обновления до версии 12 [рекомендации уровня служб](sql-database-service-tier-advisor.md) и [рекомендации пула эластичных баз данных](sql-database-elastic-pool-create-portal.md) не будут доступны, пока служба не выполнит оценку рабочих нагрузок на новом сервере. Журнал рекомендаций сервера версии 11 не применяется к серверам версии 12, поэтому он не сохраняется.

## Подготовка к обновлению

- **Обновление всех баз данных Web и Business**: см. раздел [Обновление всех баз данных Web и Business](sql-database-v12-upgrade.md#upgrade-all-web-and-business-databases) ниже или воспользуйтесь [PowerShell для обновления баз данных и сервера](sql-database-upgrade-server-powershell.md).
- **Обзор и приостановка георепликации**: если ваша база данных SQL настроена для георепликации, то необходимо задокументировать ее текущую конфигурацию и [остановить георепликацию](sql-database-geo-replication-portal.md#remove-secondary-database). После завершения обновления потребуется перенастроить базу данных для георепликации.
- **Откройте следующие порты, если у вас есть клиенты на виртуальной машине Azure**: если клиентская программа подключается к базе данных SQL версии 12, а клиент работает на виртуальной машине Azure, необходимо открыть на ней диапазоны портов 11 000–11 999 и 14 000–14 999. Дополнительные сведения см. в разделе [Порты для базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Предварительные требования

Чтобы обновить сервер до версии 12 с помощью PowerShell, сначала установите и запустите последнюю версию Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


## Настройка учетных данных и выбор подписки

Для выполнения командлетов PowerShell в подписке Azure необходимо сначала настроить доступ к учетной записи Azure. Выполните приведенную ниже команду. Откроется страница входа, на которой необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.

Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать на предыдущем шаге или, если у вас несколько подписок, выполнить командлет **Get-AzureRmSubscription** и скопировать необходимые сведения о подписке из набора результатов.

Выполните следующий командлет с данными о подписке, чтобы задать текущую подписку.

	Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Следующие команды будет выполнены для заданной выше подписки.

## Получение рекомендаций

Для получения рекомендаций по обновлению сервера выполните следующий командлет:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Дополнительные сведения см. в статьях [Создание пула эластичных баз данных](sql-database-elastic-pool-create-portal.md) и [Рекомендации по выбору ценовой категории для базы данных SQL Azure](sql-database-service-tier-advisor.md).



## Начало обновления

Чтобы запустить обновление сервера, выполните следующий командлет:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Выполнение этой команды запускает процесс обновления. Вы можете настроить вид этой рекомендации и передать измененную рекомендацию в командлет.


## Обновления сервера


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Настраиваемое сопоставление обновления

Если эти рекомендации не подходят для вашего сервера и организации, вы можете выбрать способ обновления ваших баз данных и сопоставить их с отдельными или эластичными базами данных.

Параметры ElasticPoolCollection и DatabaseCollection являются необязательными:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## Мониторинг баз данных после обновления базы данных SQL до версии 12


После обновления рекомендуется регулярно отслеживать состояние базы данных, чтобы обеспечить надлежащую производительность и оптимизировать использование.

В дополнение к наблюдению за отдельными базами данных можно отслеживать пулы эластичных баз данных с помощью [портала](sql-database-elastic-pool-manage-portal.md) или [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Данные о потреблении ресурсов**. Для баз данных уровней "Базовый", "Стандартный" и "Премиум" более точные данные о потреблении ресурсов доступны в динамическом административном представлении [sys.dm_ db_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) в базе данных пользователя. Это динамическое административное представление предоставляет данные о потреблении практически в реальном времени с детализацией предыдущего часа работы с шагом в 15 секунд. Процентное потребление DTU за интервал вычисляется как максимальное относительное потребление показателей ЦП, операций ввода-вывода и журнала. Ниже приведен запрос, который вычисляет среднее относительное потребление DTU за последний час:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Дополнительная информация о мониторинге:

- [Руководство по производительности базы данных SQL Azure для отдельных баз данных](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Вопросы цены и производительности для пула эластичных баз данных](sql-database-elastic-pool-guidance.md).
- [Мониторинг базы данных SQL Azure с помощью динамических представлений управления](sql-database-monitoring-with-dmvs.md)



**Оповещения:** настройте оповещения на портале Azure, чтобы получать уведомления о приближении показателя потребления DTU для обновленной базы данных к заданному максимальному уровню. На портале Azure можно настроить оповещения базы данных для различных метрик производительности, например DTU, ЦП, ввода-вывода и журнала. Перейдите к своей базе данных и выберите **Правила оповещения** в колонке **Параметры**.

Например, можно настроить оповещение в сообщении электронной почты об относительном потреблении DTU, если среднее относительное потребление DTU превышает 75 % за последние 5 минут. См. статью [Получение уведомлений об оповещениях](../azure-portal/insights-receive-alert-notifications.md), чтобы больше узнать о настройке уведомлений об оповещениях.



## Дальнейшие действия

- [Создайте пул эластичных баз данных](sql-database-elastic-pool-create-portal.md) и добавьте в него некоторые или все свои базы данных.
- [Измените уровень служб и уровень производительности вашей базы данных](sql-database-scale-up.md).



## Сопутствующая информация

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=AcomDC_0921_2016-->