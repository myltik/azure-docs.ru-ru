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
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: e38c42ea1c078f9f2e11ae51be37d66671392828


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

В этой статье показано, как экспортировать базу данных SQL Server в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

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
* [Newest version of SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Import a BACPAC to Azure SQL Database using SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Import a BACPAC to Azure SQL Database SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Import a BACPAC to Azure SQL Database Azure portal](sql-database-import.md)
* [Import a BACPAC to Azure SQL Database PowerShell](sql-database-import-powershell.md)

## Additional resources
* [SQL Database features](sql-database-features.md)
* [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


