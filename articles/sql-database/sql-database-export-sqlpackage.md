---
title: "SqlPackage. Экспорт базы данных SQL Server в BACPAC-файл (Azure) | Документация Майкрософт"
description: "В этой статье показано, как экспортировать базу данных SQL Server в BACPAC-файл, используя программу командной строки SqlPackage."
keywords: "База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: a17bfa6868ac581dc5ae562b3174f28571d52698


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Экспорт базы данных SQL Azure или SQL Server в BACPAC-файл с использованием SqlPackage

В этой статье показано, как экспортировать базу данных SQL Azure или SQL Server в BACPAC-файл, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

Общие сведения об экспорте в BACPAC-файл см. в [этой статье](sql-database-export.md).

> [!NOTE]
> Вы также можете экспортировать файл базы данных SQL Azure в BACPAC-файл с помощью [портала Azure](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) или [PowerShell](sql-database-export-powershell.md).
>

## <a name="sqlpackage-utility"></a>Служебная программа SQLPackage

1. Откройте окно командной строки и измените каталог, содержащий служебную программу командной строки sqlpackage.exe. Эта служебная программа поставляется в комплекте с Visual Studio и SQL Server. С помощью функции поиска на компьютере найдите путь к своей среде.
2. Выполните следующую команду sqlpackage.exe со следующими аргументами для вашей среды:
   
```    sqlpackage.exe /Action:Export /ssn:<имя_сервера> /sdn:<имя_БД> /tf:<целевой_файл>
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps

* To learn about importing a BACPAC using SQLPackage, see [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)
* To learn about importing a BACPAC using the Azure portal, see [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)
* To learn about importing a BACPAC using PowerShell, see [Import a BACPAC to Azure SQL Database using PowerShell](sql-database-import-powershell.md)
* For a discussion of the entire SQL Server database migration process, including performance recommendations, see [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md).
* To learn about long-term backup retention of an Azure SQL database backup as an alternative to exported a database for archive purposes, see [Long term backup retention](sql-database-long-term-retention.md)
* To learn about importing a BACPAC to a SQL Server database, see [Import a BACPCAC to a SQL Server database](https://msdn.microsoft.com/library/hh710052.aspx)





<!--HONumber=Feb17_HO2-->


