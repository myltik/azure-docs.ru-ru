---
title: "Создание серверов и баз данных SQL Azure и управление ими | Документация Майкрософт"
description: "Изучите основные понятия, связанные с сервером базы данных SQL Azure и базой данных SQL Azure, а также узнайте о том, как создать серверы и базы данных и управлять ими с помощью портала Azure, PowerShell, Azure CLI, Transact-SQL и REST API."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 8a44f10eda396aec72e05e87e406ff80834294d8
ms.contentlocale: ru-ru
ms.lasthandoff: 08/30/2017

---

# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Создание серверов базы данных SQL Azure и баз данных SQL Azure и управление ими

База данных SQL Azure — это управляемая база данных в Microsoft Azure, которая создается внутри [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) с определенным набором [вычислительных ресурсов и ресурсов хранилища для разных рабочих нагрузок](sql-database-service-tiers.md). База данных SQL Azure связана с логическим сервером базы данных SQL Azure, который создан в определенном регионе Azure. 

## <a name="an-azure-sql-database-can-be-a-single-pooled-or-partitioned-database"></a>База данных SQL Azure может быть отдельной базой данных, базой данных в составе пула или секционированной базой данных.

База данных SQL Azure может:

- представлять собой отдельную базу данных с [собственным набором ресурсов](sql-database-single-database-resources.md);
- входить в состав [эластичного пула](sql-database-elastic-pool.md), в котором набор ресурсов используется совместно;
- входить в состав [масштабируемого набора сегментированных баз данных](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), которые могут быть отдельными базами данных или частью пула;
- входить в состав набора баз данных, использующихся в [шаблоне разработки для мультитенантных приложений SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md), которые могут быть отдельными базами данных или частью пула (или этот набор может иметь смешанную конфигурацию). 

> [!TIP]
> Допустимые имена баз данных см. в статье об [идентификаторах базы данных](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers). 
>
 
- Параметры сортировки базы данных по умолчанию, используемые базой данных SQL Microsoft Azure, — это **SQL_LATIN1_GENERAL_CP1_CI_AS**, где **LATIN1_GENERAL** — английский язык (США), **CP1** — кодовая страница 1252, **CI** выполняется без учета регистра, а **AS** означает, что учитываются диакритические знаки. Дополнительные сведения о настройке параметров сортировки см. в статье [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).
- База данных SQL Microsoft Azure поддерживает клиент протокола для потока табличных данных (TDS) версии 7.3 или более поздней.
- Разрешены только подключения по протоколу TCP/IP.

## <a name="what-is-an-azure-sql-logical-server"></a>Что такое логический сервер SQL Azure?

Логический сервер выступает в качестве центра администрирования нескольких баз данных, включая [эластичные пулы](sql-database-elastic-pool.md), [имена для входа](sql-database-manage-logins.md), [правила брандмауэра](sql-database-firewall-configure.md), [правила аудита](sql-database-auditing.md), [политики обнаружения угроз](sql-database-threat-detection.md) и [группы отработки отказа](sql-database-geo-replication-overview.md). Логический сервер может находиться в регионе, отличном от региона своей группы ресурсов. Логический сервер должен существовать еще до создания базы данных SQL Azure. Все базы данных на сервере создаются в том же регионе, что логический сервер. 


> [!IMPORTANT]
> В базе данных SQL сервер — это логическая конструкция, отличающаяся от экземпляра SQL Server, с которым вы могли работать в локальной среде. В частности, служба базы данных SQL не дает никаких гарантий, связанных с расположением баз данных относительно логических серверов, а также не предоставляет доступ или возможности на уровне экземпляра.
> 

При создании логического сервера следует указать учетные данные входа на сервер, имеющие права администратора для базы данных master на этом сервере и всех баз данных, созданных на этом сервере. Эта начальная учетная запись является учетной записью входа SQL. База данных SQL Azure поддерживает аутентификацию SQL и аутентификацию Azure Active Directory. Дополнительную информацию об именах для входа и аутентификации можно найти в разделе [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md). Проверка подлинности Windows не поддерживается. 

> [!TIP]
> Допустимые имена групп и ресурсов описываются в статье о [правилах и ограничениях именования](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

Характеристики логического сервера базы данных SQL Azure

- Создается в рамках подписки Azure, но сам логический сервер и все его ресурсы можно переместить в другую подписку.
- Выступает в качестве родительского ресурса для баз данных, эластичных пулов и хранилищ данных.
- Предоставляет пространство имен для баз данных, эластичных пулов и хранилищ данных.
- Представляет собой логический контейнер со строгой семантикой времени существования (при удалении сервера удаляются также все расположенные на нем базы данных, эластичные пулы и хранилища данных).
- Участвует в [управлении доступом на основе ролей Azure](/active-directory/role-based-access-control-what-is) (базы данных, эластичные пулы и хранилища данных, содержащиеся на сервере, наследуют его права доступа).
- Выступает в качестве элемента высокого приоритета при идентификации баз данных, эластичных пулов и хранилищ данных для управления ресурсами Azure (см. схему URL-адресов для баз данных и пулов).
- Выравнивает ресурсы в регионе.
- Предоставляет конечную точку подключения к базе данных (<serverName>.database.windows.net).
- Предоставляет доступ к метаданным ресурсов через динамические административные представления путем подключения к базе данных master. 
- Обеспечивает область для политик управления, применяемых к базам данных: имена для входа, брандмауэр, аудит, обнаружение угроз и т. д. 
- Ограничен квотой в рамках родительской подписки (по умолчанию шесть серверов на подписку). Ограничения подписки см. [здесь](../azure-subscription-service-limits.md).
- Обеспечивает область для квоты базы данных и квоты DTU для ресурсов, содержащихся на нем (например, 45 000 DTU).
- Выступает в качестве области управления версиями для компонентов, включенных для его ресурсов. 
- За счет возможностей входа в систему с использованием субъекта серверного уровня может управлять всеми базами данных на сервере.
- Может содержать имена входов, подобные именам входов экземпляров SQL Server в локальной среде с доступом к одной или нескольким базам данных на сервере. Можно предоставлять ограниченные права администратора. Дополнительные сведения см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Базы данных SQL Azure, защищенные брандмауэром базы данных SQL

Чтобы защитить данные, [брандмауэр базы данных SQL](sql-database-firewall-configure.md) предотвращает доступ к серверу базы данных и всем его базам данных, осуществляемый вне подключения к серверу, установленного непосредственно через подписку Azure. Чтобы обеспечить дополнительные возможности подключения, необходимо [создать одно или несколько правил брандмауэра](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Изучите дополнительные сведения о создании [эластичных пулов](sql-database-elastic-pool.md) и управлении ими.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Управление серверами SQL Azure, базами данных и брандмауэрами с помощью портала Azure

Группу ресурсов базы данных SQL Azure можно создать заблаговременно или при создании самого сервера. Существует несколько методов перехода к форме создания сервера SQL Server. Можно создать сервер SQL Server отдельно или при создании базы данных. 

### <a name="create-a-blank-sql-server-logical-server"></a>Создание пустого сервера SQL Server (логического сервера)

Чтобы создать сервер базы данных SQL Azure (без базы данных) с помощью [портала Azure](https://portal.azure.com), перейдите к пустой форме "SQL Server (логический сервер)". На следующем снимке экрана показан один из способов открытия формы для создания пустого логического сервера SQL Server. 

   ![Заполненная форма создания логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

Если вы перешли к этой форме другим способом, данные в ней будут аналогичные.

### <a name="create-a-blank-or-sample-sql-database"></a>Создание пустой базы данных SQL или примера базы данных SQL

Чтобы создать базу данных SQL Azure с помощью [портала Azure](https://portal.azure.com), перейдите к пустой форме "База данных SQL" и введите необходимую информацию. Группу ресурсов базы данных SQL Azure и логический сервер можно создать заблаговременно или при создании самой базы данных. Можно создать пустую базу данных или пример базы данных на основе Adventure Works LT. 

  ![Создание базы данных — 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Сведения о выборе ценовой категории для базы данных см. в разделе [Доступные параметры производительности базы данных SQL Azure](sql-database-service-tiers.md).
>

### <a name="manage-an-existing-sql-server"></a>Управление существующим сервером SQL Server

Для управления существующим сервером перейдите к нему, воспользовавшись одним из множества способов. Например, это можно сделать со страницы определенной базы данных SQL, страницы **Серверы SQL Server** или страницы **Все ресурсы**. На следующем снимке экрана показано, как можно начать настройку брандмауэра уровня сервера на странице **Обзор** для сервера. 

   ![Общие сведения о логическом сервере](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

Для управления существующей базой данных перейдите на страницу **Базы данных SQL** и щелкните нужную базу данных. На следующем снимке экрана показано, как можно начать настройку брандмауэра уровня сервера для базы данных на странице **Обзор** для базы данных. 

   ![правило брандмауэра для сервера](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Настройка свойств производительности для базы данных описывается в разделе [Доступные параметры производительности базы данных SQL Azure](sql-database-service-tiers.md).
>

> [!TIP]
> Быстрое руководство по порталу Azure приведено в разделе [Создание базы данных SQL Azure на портале Azure](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Управление серверами SQL Azure, базами данных и брандмауэрами с помощью PowerShell

Для создания сервера Azure SQL, баз данных и брандмауэров и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, прочитайте статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Изучите дополнительные сведения о создании [эластичных пулов](sql-database-elastic-pool.md) и управлении ими.

| Командлет | Описание |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Создает базу данных. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Получает одну или несколько баз данных.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Определяет свойства базы данных или перемещает ее в эластичный пул.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Удаляет базу данных.|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Создает группу ресурсов.
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Создает сервер.|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Возвращает сведения о серверах.|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/set-azurermsqlserver)|Изменяет свойства сервера.|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Удаляет сервера.|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Создает правило брандмауэра уровня сервера. |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Получает правила брандмауэра для сервера.|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Изменяет правило брандмауэра на сервере.|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Удаляет правило брандмауэра с сервера.|

> [!TIP]
> Краткое руководство по PowerShell приведено в разделе [Создание отдельной базы данных SQL Azure с помощью PowerShell](sql-database-get-started-portal.md). Образцы скриптов PowerShell, см. в статьях [Создание отдельной базы данных SQL и настройка правила брандмауэра с помощью PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) и [Мониторинг и масштабирование отдельной базы данных SQL с помощью PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Управление серверами SQL Azure, базами данных и брандмауэрами с помощью Azure CLI

Для создания сервера Azure SQL, баз данных и брандмауэров и управления ими с помощью [Azure CLI](/cli/azure/overview) используйте приведенные ниже команды [Azure CLI для Базы данных SQL](/cli/azure/sql/db). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows. Изучите дополнительные сведения о создании [эластичных пулов](sql-database-elastic-pool.md) и управлении ими.

| Командлет | Описание |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |Создает базу данных.|
|[az sql db list](/cli/azure/sql/db#list)|Выводит список всех баз данных и хранилищ данных на сервере или всех баз данных в эластичном пуле.|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Выводит список доступных целевых служб и ограничений хранилища.|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Возвращает данные об использовании баз данных.|
|[az sql db show](/cli/azure/sql/db#show)|Возвращает сведения о базе данных или хранилище данных.|
|[az sql db update](/cli/azure/sql/db#update)|Обновляет базу данных.|
|[az sql db delete](/cli/azure/sql/db#delete)|Удаляет базу данных.|
|[az group create](/cli/azure/group#create)|Создает группу ресурсов.|
|[az sql server create](/cli/azure/sql/server#create)|Создает сервер.|
|[az sql server list](/cli/azure/sql/server#list)|Выводит список серверов.|
|[az sql server list-usages](/cli/azure/sql/server#list-usages)|Возвращает данные об использовании серверов.|
|[az sql server show](/cli/azure/sql/server#show)|Возвращает сервер.|
|[az sql server update](/cli/azure/sql/server#update)|Обновляет сервер.|
|[az sql server delete](/cli/azure/sql/server#delete)|Удаляет сервер.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#create)|Создает правило брандмауэра для сервера.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#list)|Выводит список правил брандмауэра на сервере.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#show)|Отображает сведения о правиле брандмауэра.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule#update)|Обновляет правило брандмауэра.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#delete)|Удаляет правило брандмауэра.|

> [!TIP]
> Краткое руководство по Azure CLI приведено в разделе [Создание отдельной базы данных SQL Azure с помощью Azure CLI](sql-database-get-started-cli.md). Образцы скриптов Azure CLI, см. в статьях [Создание отдельной базы данных SQL и настройка правила брандмауэра с помощью интерфейса командной строки](scripts/sql-database-create-and-configure-database-cli.md) и [Мониторинг и масштабирование отдельной базы данных SQL с помощью интерфейса командной строки](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Управление серверами SQL Azure, базами данных и брандмауэрами с помощью Transact-SQL

Для создания сервера Azure SQL, баз данных и брандмауэров и управления ими с помощью Transact-SQL используйте приведенные ниже команды T-SQL. Можно выполнить эти команды на портале Azure, в [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программе, которая может подключаться к серверу базы данных SQL Azure и передавать команды Transact-SQL. Ознакомьтесь с дополнительными сведениями об управлении [эластичными пулами](sql-database-elastic-pool.md).

> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать или удалить сервер.
>

| Команда | Описание |
| --- | --- |
|[CREATE DATABASE (база данных SQL Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Создает базу данных. Для создания базы данных требуется подключение к базе данных master.|
| [ALTER DATABASE (база данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Изменяет базу данных SQL Azure. |
|[ALTER DATABASE (хранилище данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Изменяет хранилище данных SQL Azure.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Удаляет базу данных.|
|[sys.database_service_objectives (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Возвращает сведения о выпуске (уровень служб), целевой службе (ценовую категорию), а также имя эластичного пула (при наличии) для базы данных SQL Azure или хранилища данных SQL Azure. В системе базы данных master на сервере базы данных SQL Azure возвращает сведения обо всех базах данных. Для использования хранилища данных SQL Azure необходимо подключиться к базе данных master.|
|[sys.dm_db_resource_stats (база данных SQL Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Возвращает сведения об использовании ЦП, операциях ввода-вывода и потреблении памяти для базы данных в базе данных SQL Azure. Новая строка создается каждые 15 секунд, даже если в базе данных не выполняется никаких действий.|
|[sys.resource_stats (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Возвращает сведения об использовании ЦП и хранилища для базы данных SQL Azure. Данные собираются и объединяются за пятиминутные интервалы.|
|[sys.database_connection_stats (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Содержит статистику о событиях подключения к базе данных в базе данных SQL, предоставляя общие сведения об успешных и неудачных попытках подключения. |
|[sys.event_log (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Возвращает сведения об успешных подключениях к базе данных в базе данных SQL Azure, а также о сбоях подключения и взаимоблокировках. С помощью этой информации можно отслеживать действия с базой данных в базе данных SQL или устранять возникшие неполадки.|
|[sp_set_firewall_rule (база данных SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Создает или обновляет параметры брандмауэра уровня сервера для сервера базы данных SQL. Эта хранимая процедура доступна только в базе данных master для имени для входа субъекта серверного уровня. Правило брандмауэра уровня сервера можно будет создать с помощью Transact-SQL только после того, как пользователь, имеющий разрешения уровня Azure, создаст первое правило брандмауэра уровня сервера.|
|[sys.firewall_rules (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Возвращает сведения о параметрах брандмауэра уровня сервера, связанных с базой данных SQL Microsoft Azure.|
|[sp_delete_firewall_rule (база данных SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Удаляет параметры брандмауэра уровня сервера с сервера базы данных SQL. Эта хранимая процедура доступна только в базе данных master для имени для входа субъекта серверного уровня.|
|[sp_set_database_firewall_rule (база данных SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Создает или обновляет правила брандмауэра уровня базы данных для базы данных SQL Azure или хранилища данных SQL. Можно настроить правила брандмауэра базы данных для базы данных master и пользовательских баз данных в Базе данных SQL. Правила брандмауэра базы данных полезны в тех случаях, когда применяются пользователи автономной базы данных. |
|[sys.database_firewall_rules (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Возвращает сведения о параметрах брандмауэра уровня базы данных, связанных с базой данных SQL Microsoft Azure. |
|[sp_delete_database_firewall_rule (база данных SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Удаляет параметр брандмауэра уровня базы данных из базы данных SQL Azure или хранилища данных SQL. |


> [!TIP]
> Краткое руководство по использованию SQL Server Management Studio в Microsoft Windows приведено в разделе [Подключайтесь к базе данных Azure SQL и создавайте запросы к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md). Краткое руководство по использованию Visual Studio Code в macOS, Linux и Windows приведено в разделе [База данных SQL Azure: подключение и запрос данных с помощью Visual Studio Code](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Управление серверами SQL Azure, базами данных и брандмауэрами с помощью REST API

Для создания сервера Azure SQL Server, баз данных и брандмауэров и управления используются приведенные ниже запросы REST API.

| Команда | Описание |
| --- | --- |
|[Серверы: создание или обновление](/rest/api/sql/servers/createorupdate)|Создает или обновляет сервер.|
|[Серверы: удаление](/rest/api/sql/servers/delete)|Удаляет сервер SQL Server.|
|[Серверы: получение](/rest/api/sql/servers/get)|Получает сервер.|
|[Серверы: вывод списка](/rest/api/sql/servers/list)|Возвращает список серверов.|
|[Серверы: вывод списка по группе ресурсов](/rest/api/sql/servers/listbyresourcegroup)|Возвращает список серверов в группе ресурсов.|
|[Серверы: обновление](/rest/api/sql/servers/update)|Обновляет существующий сервер.|
|[Серверы: SQL](/rest/api/sql/servers%20-%20sql)|Определяет, может ли быть создан ресурс с указанным именем.|
|[Базы данных: создание или обновление](/rest/api/sql/databases/createorupdate)|Создает новую базу данных или обновляет имеющуюся.|
|[Базы данных: получение](/rest/api/sql/databases/get)|Получает базу данных.|
|[Базы данных: получение по эластичному пулу](/rest/api/sql/databases/getbyelasticpool)|Получает базу данных в эластичном пуле.|
|[Базы данных: получение по рекомендуемому эластичному пулу](/rest/api/sql/databases/getbyrecommendedelasticpool)|Получает базу данных в рекомендуемом эластичном пуле.|
|[Базы данных: вывод списка по эластичному пулу](/rest/api/sql/databases/listbyelasticpool)|Возвращает список баз данных в пуле эластичных баз данных.|
|[Базы данных: вывод списка по рекомендуемому эластичному пулу](/rest/api/sql/databases/listbyrecommendedelasticpool)|Возвращает список баз данных в рекомендуемом эластичном пуле.|
|[Базы данных: вывод списка по серверу](/rest/api/sql/databases/listbyserver)|Возвращает список баз данных на сервере.|
|[Базы данных: обновление](/api/sql/databases/update)|Обновляет имеющуюся базу данных.|
|[Правила брандмауэра: создание и изменение](/rest/api/sql/firewallrules/createorupdate)|Создает или обновляет правило брандмауэра.|
|[Правила брандмауэра: удаление](/rest/api/sql/firewallrules/delete)|Удаляет правило брандмауэра.|
|[Правила брандмауэра: получение](/rest/api/sql/firewallrules/get)|Получает правило брандмауэра.|
|[Правила брандмауэра: вывод списка по серверу](/rest/api/sql/firewallrules/listbyserver)|Возвращает список правил брандмауэра.|

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше об объединении баз данных в эластичные пулы, ознакомьтесь с разделом [Эластичные пулы помогают управлять несколькими базами данных SQL и масштабировать их](sql-database-elastic-pool.md).
- Сведения о службе базы данных SQL Azure см. в статье [Функции службы базы данных SQL Azure](sql-database-technical-overview.md).
- Чтобы узнать о переносе базы данных SQL Server в Azure, ознакомьтесь с разделом [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
- Дополнительные сведения о поддерживаемых функциях см. [здесь](sql-database-features.md).

