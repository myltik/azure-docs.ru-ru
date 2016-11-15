---
title: "Создание хранилища данных SQL с помощью TSQL | Документация Майкрософт"
description: "Сведения о создании хранилища данных SQL Azure с помощью TSQL"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 443e74834a7181560e812ce31db460ef2c4ff911


---
# <a name="create-a-sql-data-warehouse-database-by-using-transactsql-tsql"></a>Создание базы данных хранилища данных SQL с помощью Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Портал Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

В этой статье объясняется, как создать хранилище данных SQL с помощью T-SQL.

## <a name="prerequisites"></a>Предварительные требования
Для начала работы необходимы перечисленные ниже компоненты и данные. 

* **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][Бесплатная пробная версия Azure] или [кредитов Azure MSDN][Кредиты Azure MSDN].
* **Azure SQL Server.** Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure][Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure] или в разделе [Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра][Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра].
* **Группа ресурсов.** Используйте ту же группу ресурсов, что и для Azure SQL Server, или [создайте группу ресурсов][cоздание группы ресурсов].
* **Среда выполнения T-SQL**. Для выполнения T-SQL можно использовать [Visual Studio][Установка Visual Studio и SSDT], [sqlcmd][sqlcmd] или [SSMS][SSMS].

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения о ценах см. на странице с [ценами на хранилище данных SQL][Цены на хранилище данных SQL].
> 
> 

## <a name="create-a-database-with-visual-studio"></a>Создание базы данных с помощью Visual Studio
Если вы не знакомы с Visual Studio, см. статью [Запросы к хранилищу данных SQL Azure (Visual Studio)][Запросы к хранилищу данных SQL Azure (Visual Studio)].  Сначала откройте обозреватель объектов SQL Server в Visual Studio и подключитесь к серверу, на котором будет размещена база данных хранилища данных SQL.  После подключения можно создать хранилище данных SQL. Для этого выполните следующую команду для базы данных **master**.  Эта команда создаст базу данных MySqlDwDb с целевой службой DW400 и позволит ей достичь максимального размера 10 ТБ.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Создание базы данных с помощью служебной программы sqlcmd
Также можно запустить ту же команду с помощью sqlcmd, выполнив следующую команду в командной строке.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Если параметры сортировки не указаны, по умолчанию используется COLLATE SQL_Latin1_General_CP1_CI_AS.  Значение параметра `MAXSIZE` может составлять от 250 ГБ до 240 ТБ.  Значение параметра `SERVICE_OBJECTIVE` задается в диапазоне от DW100 до DW2000 единиц [DWU][DWU].  Список всех допустимых значений см. в документации MSDN [CREATE DATABASE (Azure SQL Data Warehouse)][CREATE DATABASE (Azure SQL Data Warehouse)] (Инструкция CREATE DATABASE (Хранилище данных SQL Azure).  Параметры MAXSIZE и SERVICE_OBJECTIVE можно изменить с помощью команды T-SQL [ALTER DATABASE][ALTER DATABASE].  Параметры сортировки базы данных нельзя изменить после создания.   При изменении параметра SERVICE_OBJECTIVE следует соблюдать осторожность, так как изменение DWU приводит к перезапуску служб, который отменяет все текущие запросы.  При изменении параметра MAXSIZE службы не перезапускаются, так как это всего лишь простая операция с метаданными.

## <a name="next-steps"></a>Дальнейшие действия
После завершения подготовки хранилища данных SQL можно [загрузить в него демонстрационные данные][загрузка демонстрационных данных] или ознакомьтесь с возможностями [разработки][разработка], [загрузки][загрузка] или [миграции][миграция].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Создание хранилища данных SQL на портале Azure]: sql-data-warehouse-get-started-provision.md
[Запросы к хранилищу данных SQL Azure (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[переноса]: sql-data-warehouse-overview-migrate.md
[разработки]: sql-data-warehouse-overview-develop.md
[загрузки]: sql-data-warehouse-overview-load.md
[загрузить демонстрационные данные]: sql-data-warehouse-load-sample-databases.md
[созданию логического сервера базы данных SQL с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[создайте новую группу ресурсов]: ../resource-group-template-deploy-portal.md#create-resource-group
[Установка Visual Studio и SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[Создание базы данных]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[ценами на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[деньги на счете в Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


