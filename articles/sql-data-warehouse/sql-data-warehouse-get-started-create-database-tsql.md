---
title: "Создание хранилища данных SQL с помощью TSQL | Документация Майкрософт"
description: "Сведения о создании хранилища данных SQL Azure с помощью TSQL"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 10d8aa2b3ab8d7d8a9b91e95ffccf03faa89d237
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Создание базы данных хранилища данных SQL с помощью Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [портале Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

В этой статье объясняется, как создать хранилище данных SQL с помощью T-SQL.

## <a name="prerequisites"></a>предварительным требованиям
Для начала работы необходимы перечисленные ниже компоненты и данные.

* **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][Azure Free Trial] или [денег на счете в Azure][MSDN Azure Credits] MSDN.
* **Azure SQL Server.** Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure][Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure] или в разделе [Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра][Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра].
* **Группа ресурсов.** Используйте ту же группу ресурсов, что и для Azure SQL Server, или [создайте группу ресурсов][how to create a resource group].
* **Среда выполнения T-SQL.** Для выполнения T-SQL можно использовать [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] или [SSMS][SSMS].

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения о ценах на хранилище данных SQL см. на [этой странице][SQL Data Warehouse pricing].
>
>

## <a name="create-a-database-with-visual-studio"></a>Создание базы данных с помощью Visual Studio
Если вы не знакомы с Visual Studio, см. статью [Запросы к хранилищу данных SQL Azure (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  Сначала откройте обозреватель объектов SQL Server в Visual Studio и подключитесь к серверу, на котором будет размещена база данных хранилища данных SQL.  После подключения можно создать хранилище данных SQL. Для этого выполните следующую команду для базы данных **master**.  Эта команда создаст базу данных MySqlDwDb с целевой службой DW400 и позволит ей достичь максимального размера 10 ТБ.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Создание базы данных с помощью служебной программы sqlcmd
Также можно запустить ту же команду с помощью sqlcmd, выполнив следующую команду в командной строке.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Если параметры сортировки не указаны, по умолчанию используется COLLATE SQL_Latin1_General_CP1_CI_AS.  Значение параметра `MAXSIZE` может составлять от 250 ГБ до 240 ТБ.  Значение параметра `SERVICE_OBJECTIVE` задается в диапазоне от DW100 до DW2000 единиц [DWU][DWU].  Список всех допустимых значений см. в документации MSDN об инструкции [CREATE DATABASE][CREATE DATABASE].  Параметры MAXSIZE и SERVICE_OBJECTIVE можно изменить с помощью команды T-SQL [ALTER DATABASE][ALTER DATABASE].  Параметры сортировки базы данных нельзя изменить после создания.   При изменении параметра SERVICE_OBJECTIVE следует соблюдать осторожность, так как изменение DWU приводит к перезапуску служб, который отменяет все текущие запросы.  При изменении параметра MAXSIZE службы не перезапускаются, так как это всего лишь простая операция с метаданными.

## <a name="next-steps"></a>Дополнительная информация
После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][load sample data] или ознакомиться с возможностями [разработки][develop], [загрузки][load] или [переноса][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
