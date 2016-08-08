<properties 
    pageTitle="Настройка активной георепликации баз данных SQL Azure с помощью PowerShell | Microsoft Azure" 
    description="Настройка активной георепликации для базы данных SQL Azure с помощью PowerShell" 
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
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# Настройка георепликации базы данных SQL Azure с помощью PowerShell

> [AZURE.SELECTOR]
- [Обзор](sql-database-geo-replication-overview.md)
- [Портал Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

В этой статье показано, как настроить активную георепликацию для базы данных SQL Azure с помощью PowerShell.

Чтобы инициировать отработку отказа с помощью PowerShell, ознакомьтесь с разделом [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Активная георепликация (с доступными для чтения базами данных-получателями) теперь доступна для всех баз данных и всех уровней обслуживания. В апреле 2017 г. недоступные для чтения базы данных-получатели будут удалены, и существующие недоступные для чтения базы данных-получатели будут автоматически преобразованы в доступные для чтения.



Для настройки активной георепликации с помощью PowerShell необходимо следующее.

- Подписка Azure.
- База данных SQL Azure — база данных-источник, которую необходимо реплицировать.
- Azure PowerShell, начиная с версии 1.0. Модули Azure PowerShell можно загрузить и установить, выполнив инструкции из раздела [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


## Настройка учетных данных и выбор подписки

Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на портал Azure.


	Login-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам нужно знать ее идентификатор. Идентификатор подписки можно скопировать из данных, которые были показаны на предыдущем этапе. Если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureRmSubscription** и скопируйте нужные данные из полученных результатов. С помощью идентификатора подписки приведенный ниже командлет задает текущую подписку:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureRmSubscription** вы вернетесь в командную строку PowerShell.


## Добавление базы данных-получателя


Следующие действия позволяют создать новую базу данных-получатель в связи георепликации.
  
Включить базу данных-получатель может только владелец или совладелец подписки.

Чтобы добавить базу данных-получатель на сервере-партнере в локальную базу данных на сервере, к которому выполняется подключение (база данных-источник), используйте командлет **New-AzureRmSqlDatabaseSecondary**.

Этот командлет заменяет параметр **Start-AzureSqlDatabaseCopy** на **–IsContinuous**. Он выдает объект **AzureRmSqlDatabaseSecondary**, который может использоваться другими командлетами для четкого определения конкретной связи репликации. Этот командлет возвращает результат после создания и полного заполнения базы данных-получателя. В зависимости от размера базы данных этот процесс может длиться от нескольких минут до нескольких часов.

Реплицированная база данных на сервере-получателе будет иметь такое же имя, как и база данных на сервере-источнике, и по умолчанию тот же уровень службы. База данных-получатель может быть доступной или недоступной для чтения, отдельной или эластичной. Дополнительные сведения см. в статьях [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) и [Уровни служб](sql-database-service-tiers.md). После создания и заполнения базы данных-получателя начинается репликация данных из базы данных-источника в новую базу-данных-получателя. Ниже описано, как выполнить эту задачу с помощью PowerShell, чтобы создать доступную или недоступную для чтения базу данных-получателя с отдельной или эластичной базой данных.

Если база данных-партнер уже создана (например, в результате прекращения предыдущей связи георепликации), выполнение команды завершится сбоем.



### Добавление недоступной для чтения отдельной базы данных-получателя

Следующая команда создает недоступную для чтения базу данных-получателя базы данных mydb сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Добавление доступной для чтения отдельной базы данных-получателя

Следующая команда создает доступную для чтения базу данных-получателя базы данных mydb сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Добавление недоступной для чтения эластичной базы данных-получателя

Следующая команда создает недоступную для чтения базу данных-получателя базы данных mydb в пуле эластичных баз данных ElasticPool1 сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Добавление доступной для чтения эластичной базы данных-получателя

Следующая команда создает доступную для чтения базу данных-получателя базы данных mydb в пуле эластичных баз данных ElasticPool1 сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Удаление базы данных-получателя

С помощью командлета **Remove-AzureRmSqlDatabaseSecondary** можно навсегда завершить отношение репликации между базой данных-получателем и ее источником. После завершения этого отношения база данных-получатель становится базой данных для чтения и записи. Если подключение к базе данных-получателю прервано, команда все равно будет выполнена. При этом получатель станет базой данных для чтения и записи только тогда, когда восстановится подключение. Дополнительные сведения см. в разделах [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) и [Уровни служб](sql-database-service-tiers.md).

Этот командлет заменяет Stop-AzureSqlDatabaseCopy для репликации.

Такое удаление эквивалентно принудительному завершению, которое удаляет канал репликации и оставляет бывшую базу данных-получателя в качестве автономной базы данных, репликация которой выполнена не полностью перед завершением работы. Все данные связи (если доступны) на предыдущем отправителе и предыдущем получателе будут очищены. Этот командлет возвращает результат при удалении связи репликации.


Чтобы удалить получателя, пользователи должны иметь доступ на запись к базе данных-источнику и базе данных-получателю в соответствии с RBAC. Дополнительные сведения см. в разделе "Контроль доступа на основе ролей".

Следующие действия удаляют связь репликации между базой данных с именем mydb и сервером srv2 из группы ресурсов rg2.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## Мониторинг конфигурации и работоспособности георепликации

К задачам мониторинга относятся мониторинг конфигурации георепликации и мониторинг работоспособности репликации данных.

Командлет [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) можно использовать для получения сведений о каналах прямой репликации, отображаемых в представлении каталога sys.geo\_replication\_links.

Следующая команда отображает состояние канала репликации между базой данных-источником mydb и базой данных-получателем на сервере srv2 из группы ресурсов rg2.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## Дальнейшие действия

- Чтобы больше узнать об активной георепликации, ознакомьтесь с разделом [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md).
- Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0727_2016-->