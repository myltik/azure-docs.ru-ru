---
title: "Настройка активной георепликации баз данных SQL Azure с помощью PowerShell | Документация Майкрософт"
description: "Настройка активной георепликации для базы данных SQL Azure с помощью PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: bc5e50e4-bbb2-4ce1-9ee5-9a632de6fa06
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/14/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 1849e257240a45a6161db524ce53a83cbf29068d


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-powershell"></a>Настройка активной георепликации для базы данных SQL Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Обзор](sql-database-geo-replication-overview.md)
> * [Портал Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

В этой статье показано, как настроить активную георепликацию для базы данных SQL Azure с помощью PowerShell.

Чтобы инициировать отработку отказа с помощью PowerShell, ознакомьтесь с разделом [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью PowerShell](sql-database-geo-replication-failover-powershell.md).

> [!NOTE]
> Активная георепликация (с доступными для чтения базами данных-получателями) теперь доступна для всех баз данных и всех уровней обслуживания. В апреле 2017 года недоступные для чтения базы данных-получатели будут удалены, и имеющиеся недоступные для чтения базы данных будут автоматически преобразованы в доступные для чтения базы данных-получатели.
> 
> 

Для настройки активной георепликации с помощью PowerShell необходимо следующее.

* Подписка Azure. 
* База данных SQL Azure — база данных-источник, которую необходимо реплицировать.
* Azure PowerShell, начиная с версии 1.0. Модули Azure PowerShell можно загрузить и установить, выполнив инструкции из раздела [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="configure-your-credentials-and-select-your-subscription"></a>Настройка учетных данных и выбор подписки
Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на портал Azure.

    Login-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.

### <a name="select-your-azure-subscription"></a>Выбор подписки Azure
Чтобы выбрать подписку, вам нужно знать ее идентификатор. Идентификатор подписки можно скопировать из данных, показанных на предыдущем этапе. Если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureRmSubscription** и скопируйте нужные данные из полученных результатов. Приведенный ниже командлет задает текущую подписку, используя идентификатор подписки.

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureRmSubscription** вы вернетесь в командную строку PowerShell.

## <a name="add-secondary-database"></a>Добавление базы данных-получателя
Следующие действия позволяют создать новую базу данных-получатель в связи георепликации.  

Включить базу данных-получатель может только владелец или совладелец подписки. 

Чтобы добавить базу данных-получатель на сервере-партнере в локальную базу данных на сервере, к которому выполняется подключение (база данных-источник), используйте командлет **New-AzureRmSqlDatabaseSecondary** . 

Этот командлет заменяет параметр **Start-AzureSqlDatabaseCopy** на **-IsContinuous**.  Он выдает объект **AzureRmSqlDatabaseSecondary** , который может использоваться другими командлетами для четкого определения конкретной связи репликации. Этот командлет возвращает результат после создания и полного заполнения базы данных-получателя. В зависимости от размера базы данных этот процесс может длиться от нескольких минут до нескольких часов.

Реплицированная база данных на сервере-получателе будет иметь такое же имя, как и база данных на сервере-источнике, и по умолчанию тот же уровень службы. База данных-получатель может быть доступной или недоступной для чтения, автономной или размещенной в эластичном пуле. Дополнительные сведения см. в статьях [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689\(v=azure.300\).aspx) и [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md).
После создания и заполнения базы данных-получателя начинается репликация данных из базы данных-источника в новую базу-данных-получателя. Ниже описано, как выполнить эту задачу с помощью PowerShell, чтобы создать доступную или недоступную для чтения базу данных-получателя с автономной или эластичной базой данных.

Если база данных-партнер уже создана (например, в результате прекращения предыдущей связи георепликации), выполнение команды завершится сбоем.

### <a name="add-a-non-readable-secondary-standalone-database"></a>Добавление недоступной для чтения автономной базы данных-получателя
Следующая команда создает недоступную для чтения базу данных-получателя базы данных mydb сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-standalone-database"></a>Добавление доступной для чтения автономной базы данных-получателя
Следующая команда создает доступную для чтения базу данных-получателя базы данных mydb сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-pool"></a>Добавление недоступной для чтения базы данных-получателя (в эластичном пуле)
Следующая команда создает недоступную для чтения базу данных-получателя базы данных mydb в эластичном пуле ElasticPool1 сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-pool"></a>Добавление доступной для чтения базы данных-получателя (в эластичном пуле)
Следующая команда создает доступную для чтения базу данных-получателя базы данных mydb в эластичном пуле ElasticPool1 сервера srv2 в группе ресурсов rg2:

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Удаление базы данных-получателя
Командлет **Remove-AzureRmSqlDatabaseSecondary** позволяет навсегда завершить отношение репликации между базой данных-получателем и ее источником. После завершения этого отношения база данных-получатель становится базой данных для чтения и записи. Если подключение к базе данных-получателю прервано, команда все равно будет выполнена. При этом получатель станет базой данных для чтения и записи только тогда, когда восстановится подключение. Дополнительные сведения см. в статьях [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457\(v=azure.300\).aspx) и [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md).

Этот командлет заменяет Stop-AzureSqlDatabaseCopy для репликации. 

Такое удаление эквивалентно принудительному завершению, которое удаляет канал репликации и оставляет бывшую базу данных-получателя в качестве автономной базы данных, репликация которой выполнена не полностью перед завершением работы. Все данные связи (если доступны) на предыдущем отправителе и предыдущем получателе будут очищены. Этот командлет возвращает результат при удалении связи репликации. 

Чтобы удалить получателя, пользователи должны иметь доступ на запись к базе данных-источнику и базе данных-получателю в соответствии с RBAC. Дополнительные сведения см. в разделе "Контроль доступа на основе ролей".

Следующие действия удаляют связь репликации между базой данных с именем mydb и сервером srv2 из группы ресурсов rg2. 

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -SecondaryResourceGroup "rg2" -PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Мониторинг конфигурации и работоспособности георепликации
К задачам мониторинга относятся мониторинг конфигурации георепликации и мониторинг работоспособности репликации данных.  

Командлет [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330\(v=azure.300\).aspx) позволяет получить сведения о каналах прямой репликации, отображаемые в представлении каталога sys.geo_replication_links.

Следующая команда отображает состояние канала репликации между базой данных-источником mydb и базой данных-получателем на сервере srv2 из группы ресурсов rg2.

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -PartnerResourceGroup "rg2” -PartnerServerName "srv2”


## <a name="next-steps"></a>Дальнейшие действия
* Чтобы больше узнать об активной георепликации, прочитайте статью [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md).
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)




<!--HONumber=Jan17_HO2-->


