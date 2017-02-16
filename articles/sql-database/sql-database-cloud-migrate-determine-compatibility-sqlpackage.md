---
title: "SQLPackage. Совместимость базы данных SQL Server Azure | Документация Майкрософт"
description: "В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для миграции с помощью программы командной строки SqlPackage."
keywords: "База данных SQL Microsoft Azure, миграция базы данных, совместимость базы данных SQL, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6728693290e2c1d6c970c60c7d8ef674764e053c
ms.openlocfilehash: 834dae604fa87f753e8d050ce7cdfa7e9613f462


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Определение совместимости Базы данных SQL с помощью SqlPackage.exe
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для переноса с помощью служебной программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Использование SqlPackage.exe
1. Откройте окно командной строки и измените каталог, содержащий самую последнюю версию sqlpackage.exe. Эта служебная программа поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

2. Выполните следующую команду SqlPackage со следующими аргументами для вашей среды:

   ```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
   ```   

   | Аргумент | Описание |
   | --- | --- |
   | <имя_сервера> |имя исходного сервера |
   | <имя_БД> |имя исходной базы данных |
   | <целевой_файл> |имя файла и расположение для файла BACPAC |
   | <имя_схемы.имя_таблицы> |таблицы, для которых данные выводятся в конечный файл |
   | <выходной_файл> |имя и расположение для выходного файла с ошибками, если таковые имеются |
   
    Используется аргумент /p:TableData, так как необходимо только проверить совместимость базы данных для экспорта в базу данных SQL Azure версии&12;, а не экспортировать данные из всех таблиц. К сожалению, аргумент экспорта для sqlpackage.exe не поддерживает извлечение без таблиц. Необходимо указать хотя бы одну небольшую таблицу. Указанный <выходной_файл> содержит отчет об ошибках. Строка&2;>&1 выводит как стандартные выходные данные, так и стандартные ошибки, полученные в результате выполнения команды в указанный выходной файл.
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Откройте выходной файл и просмотрите проблемы совместимости (если они есть). 
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Скачать SQL Server Data Tools (SSDT),](https://msdn.microsoft.com/library/mt204009.aspx)
  [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Устранение проблем совместимости при миграции базы данных](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Возможности базы данных SQL Azure](sql-database-features.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO2-->


