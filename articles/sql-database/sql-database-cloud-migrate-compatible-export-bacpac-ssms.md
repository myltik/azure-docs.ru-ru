
---
title: Экспорт базы данных SQL Server в BACPAC-файл с помощью SQL Server Management Studio | Microsoft Docs
description: 'База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, мастер экспорта приложений на уровне данных '
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 08/16/2016
ms.author: carlrab

---
# Экспорт базы данных SQL Server в BACPAC-файл с помощью SQL Server Management Studio
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> 
> 

В этой статье показано, как экспортировать базу данных SQL Server в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл, используя мастер экспорта приложений на уровне данных в среде SQL Server Management Studio.

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.
   
   > [!IMPORTANT]
   > Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Откройте среду Management Studio и подключитесь к исходной базе данных в обозревателе объектов.
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. В обозревателе объектов щелкните правой кнопкой мыши исходную базу данных, наведите указатель мыши на пункт **Задачи** и выберите команду **Экспорт приложения уровня данных...**.
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. В окне мастера экспорта настройте сохранение BACPAC-файла на локальный диск или в большой двоичный объект Azure. Экспортированный файл BACPAC всегда содержит полную схему базы данных и по умолчанию данные из всех таблиц. Воспользуйтесь вкладкой "Дополнительно", если необходимо исключить данные из некоторых или всех таблиц. Например, можно экспортировать только данные для ссылочных таблиц, а не для всех таблиц.

***Важно***. При экспорте BACPAC-файла в хранилище BLOB-объектов используйте хранилище уровня "Стандартный". Импорт BACPAC-файла из хранилища уровня "Премиум" не поддерживается.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью портала Azure](sql-database-import.md)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью PowerShell](sql-database-import-powershell.md)

## Дополнительные ресурсы
* [База данных SQL версии 12.](sql-database-v12-whats-new.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL](sql-database-transact-sql-information.md).
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0817_2016-->