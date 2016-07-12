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
   ms.date="07/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание базы данных хранилища данных SQL с помощью Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

В этой статье объясняется, как создать базу данных хранилища данных SQL с помощью Transact-SQL (TSQL).

## Предварительные требования
Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

- **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][] или [кредитов Azure MSDN][].
- **Сервер SQL Azure версии 12.** См. разделы, посвященные [созданию логического сервера базы данных SQL с помощью портала Azure][] или [с помощью PowerShell][].
- **Имя группы ресурсов.** Используйте ту же группу ресурсов, что и для сервера SQL Azure версии 12, или создайте новую группу ресурсов, следуя инструкциям из [этой статьи][].
- **Visual Studio с инструментами SQL Server Data Tools**. Инструкции по установке см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL][].

> [AZURE.NOTE] Создание хранилища данных SQL может привести к дополнительным расходам. Дополнительные сведения о ценах см. на странице [Цены на хранилище данных SQL][].

## Создание базы данных с помощью Visual Studio

Если вы не знакомы с Visual Studio, см. статью [Подключение к хранилищу данных SQL с помощью Visual Studio][]. Сначала откройте обозреватель объектов SQL Server в Visual Studio и подключитесь к серверу, на котором будет размещена база данных хранилища данных SQL. После подключения можно создать хранилище данных SQL. Для этого выполните следующую команду для базы данных **master**. Эта команда создаст базу данных MySqlDwDb с целевой службой DW400 и позволит ей достичь максимального размера 10 ТБ.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Создание базы данных с помощью служебной программы sqlcmd

Также можно запустить ту же команду с помощью sqlcmd, выполнив следующую команду в командной строке.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Значение параметра `MAXSIZE` может составлять от 250 ГБ до 240 ТБ. Значение параметра `SERVICE_OBJECTIVE` задается в диапазоне от DW100 до DW2000 единиц [DWU][]. Список всех допустимых значений см. в документации MSDN об инструкции [CREATE DATABASE][]. Параметры MAXSIZE и SERVICE\_OBJECTIVE также можно изменить с помощью команды T-SQL [ALTER DATABASE][]. При изменении параметра SERVICE\_OBJECTIVE следует соблюдать осторожность, так как это приводит к перезапуску служб, при котором отменяются все текущие запросы. При изменении параметра MAXSIZE службы не перезапускаются, так как это всего лишь простая операция с метаданными.

## Дальнейшие действия
После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] или [переноса][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: ./sql-data-warehouse-get-started-provision.md
[Подключение к хранилищу данных SQL с помощью Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[переноса]: ./sql-data-warehouse-overview-migrate.md
[разработки]: ./sql-data-warehouse-overview-develop.md
[загрузки]: ./sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные]: ./sql-data-warehouse-get-started-load-sample-databases.md
[созданию логического сервера базы данных SQL с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[с помощью PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[этой статьи]: ../azure-portal/resource-group-portal.md
[Установка Visual Studio 2015 и SSDT для хранилища данных SQL]: ./sql-data-warehouse-install-visual-studio.md


<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Цены на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[кредитов Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0706_2016-->