<properties
   pageTitle="Создание хранилища данных SQL с помощью TSQL | Microsoft Azure"
   description="Сведения о создании хранилища данных SQL Azure с помощью TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание базы данных хранилища данных SQL с помощью Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

В этой статье объясняется, как создать хранилище данных SQL с помощью T-SQL.

## Предварительные требования

Для начала работы необходимы перечисленные ниже компоненты и данные.

- **Учетная запись Azure**. Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][] или [кредитов Azure MSDN][].
- **Сервер SQL Azure**. См. сведения в статьях, посвященных [созданию логического сервера базы данных SQL с помощью портала Azure][] или [с помощью PowerShell][].
- **Группа ресурсов**. Используйте ту же группу ресурсов, что и для Azure SQL Server, или [создайте новую группу ресурсов][].
- **Среда выполнения T-SQL**. Для выполнения T-SQL можно использовать [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][] или [SSMS][].

> [AZURE.NOTE] Создание хранилища данных SQL может привести к дополнительным расходам. Дополнительные сведения о ценах см. на странице [цен на хранилище данных SQL][].

## Создание базы данных с помощью Visual Studio

Если вы не знакомы с Visual Studio, см. статью [Запросы к хранилищу данных SQL Azure (Visual Studio)][]. Сначала откройте обозреватель объектов SQL Server в Visual Studio и подключитесь к серверу, на котором будет размещена база данных хранилища данных SQL. После подключения можно создать хранилище данных SQL. Для этого выполните следующую команду для базы данных **master**. Эта команда создаст базу данных MySqlDwDb с целевой службой DW400 и позволит ей достичь максимального размера 10 ТБ.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Создание базы данных с помощью служебной программы sqlcmd

Также можно запустить ту же команду с помощью sqlcmd, выполнив следующую команду в командной строке.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Если параметры сортировки не указаны, по умолчанию используется COLLATE SQL\_Latin1\_General\_CP1\_CI\_AS. Значение параметра `MAXSIZE` может составлять от 250 ГБ до 240 ТБ. Значение параметра `SERVICE_OBJECTIVE` задается в диапазоне от DW100 до DW2000 единиц [DWU][]. Список всех допустимых значений см. в документации MSDN об инструкции [CREATE DATABASE][]. Параметры MAXSIZE и SERVICE\_OBJECTIVE можно изменить с помощью команды T-SQL [ALTER DATABASE][]. Параметры сортировки базы данных нельзя изменить после создания. При изменении параметра SERVICE\_OBJECTIVE следует соблюдать осторожность, так как изменение DWU приводит к перезапуску служб, который отменяет все текущие запросы. При изменении параметра MAXSIZE службы не перезапускаются, так как это всего лишь простая операция с метаданными.

## Дальнейшие действия

После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] или [переноса][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Запросы к хранилищу данных SQL Azure (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[переноса]: sql-data-warehouse-overview-migrate.md
[разработки]: sql-data-warehouse-overview-develop.md
[загрузки]: sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные]: sql-data-warehouse-load-sample-databases.md
[созданию логического сервера базы данных SQL с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[с помощью PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[создайте новую группу ресурсов]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

.<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[цен на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[кредитов Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->