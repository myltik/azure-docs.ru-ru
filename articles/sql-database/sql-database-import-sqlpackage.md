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
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>Импорт базы данных из BACPAC-файла в базу данных SQL Azure с помощью SqlPackage

В этой статье показано, как импортировать данные из [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файла в базу данных SQL, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

> [!NOTE]
> При выполнении описанных ниже действий предполагается, что вы уже подготовили сервер Базы данных SQL, имеете сведения о подключении и проверили совместимость исходной базы данных.
> 
> 

## <a name="import-the-database"></a>Импорт базы данных
Используйте служебную программу командной строки [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) для импорта совместимой базы данных SQL Server (или базы данных SQL Azure) из BACPAC-файла.

> [!NOTE]
> При выполнении указанных ниже действий предполагается, что вы уже подготовили сервер базы данных SQL Azure и имеете сведения о подключении.
>  

В командной строке в каталоге, содержащем последнюю версию служебной программы sqlpackage.exe, выполните команду, аналогичную приведенной ниже, чтобы импортировать BACPAC-файл в базу данных SQL Azure как базу данных уровня "Премиум" с уровнем производительности Р11.

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>Дальнейшие действия

* Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-cloud-migrate.md).
* Справочные материалы о SQLPackage см. в статье об [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).
* Чтобы скачать последнюю версию SQLPackage, см. страницу [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876).
* Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).




<!--HONumber=Feb17_HO2-->


