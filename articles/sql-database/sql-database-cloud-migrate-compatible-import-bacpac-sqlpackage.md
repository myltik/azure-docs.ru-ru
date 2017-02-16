---
title: "SqlPackage. Импорт из BACPAC-файла в базу данных SQL Azure | Документация Майкрософт"
description: "В этой статье показано, как импортировать данные из BACPAC-файла в базу данных SQL, используя программу командной строки SqlPackage."
keywords: "База данных SQL Microsoft Azure, миграция базы данных, импорт базы данных, импорт BACPAC-файла, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
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
ms.openlocfilehash: 048a8f333eb555d860df0fd485ec8d53c27e3cd6


---
# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Импорт из BACPAC-файла в Базу данных SQL с помощью SqlPackage
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Портал Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

В этой статье показано, как импортировать данные из [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файла в базу данных SQL, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

> [!NOTE]
> При выполнении описанных ниже действий предполагается, что вы уже подготовили сервер Базы данных SQL, имеете сведения о подключении и проверили совместимость исходной базы данных.
> 
> 

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Импорт из BACPAC-файла в базу данных SQL Azure с помощью SqlPackage
Выполните следующие действия для импорта совместимой базы данных SQL Server (или Базы данных SQL Azure) из BACPAC-файла с помощью программы командной строки [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) .

> [!NOTE]
> При выполнении указанных ниже действий предполагается, что вы уже подготовили сервер базы данных SQL Azure и имеете сведения о подключении.
> 
> 

1. Откройте окно командной строки и измените каталог, содержащий служебную программу командной строки sqlpackage.exe. Эта служебная программа поставляется в комплекте с Visual Studio и SQL Server.
2. Выполните следующую команду sqlpackage.exe со следующими аргументами для вашей среды:
   
    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`
   
   | Аргумент | Описание |
   | --- | --- |
   | <имя_сервера> |имя конечного сервера |
   | <имя_БД> |имя конечной базы данных |
   | <имя_пользователя> |имя пользователя на конечном сервере |
   | <пароль> |пароль пользователя |
   | <исходный_файл> |имя файла и расположение импортируемого файла BACPAC |
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Возможности базы данных SQL Azure](sql-database-features.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


