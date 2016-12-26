---
title: "Экспорт базы данных SQL Server в BACPAC-файл с помощью SQL Server Management Studio | Документация Майкрософт"
description: "База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, мастер экспорта приложений на уровне данных "
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: f6f64479f6ddfda4badb8e826e03369c476d6d1d


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Экспорт базы данных SQL Server в BACPAC-файл с помощью SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

В этой статье показано, как экспортировать базу данных SQL Server в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -файл, используя мастер экспорта приложений на уровне данных в среде SQL Server Management Studio. 

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.
   
   > [!IMPORTANT]
   > Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Откройте среду Management Studio и подключитесь к исходной базе данных в обозревателе объектов.
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. В обозревателе объектов щелкните правой кнопкой мыши исходную базу данных, наведите указатель мыши на пункт **Задачи** и выберите команду **Экспорт приложения уровня данных...**
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. В окне мастера экспорта настройте сохранение BACPAC-файла на локальный диск или в большой двоичный объект Azure. Экспортированный файл BACPAC всегда содержит полную схему базы данных и по умолчанию данные из всех таблиц. Воспользуйтесь вкладкой "Дополнительно", если необходимо исключить данные из некоторых или всех таблиц. Например, можно экспортировать только данные для ссылочных таблиц, а не для всех таблиц.

    ![Параметры экспорта](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > При экспорте BACPAC-файла в хранилище BLOB-объектов Azure используйте хранилище уровня "Стандартный". Импорт BACPAC-файла из хранилища уровня "Премиум" не поддерживается.
   >
   
## <a name="next-steps"></a>Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью портала Azure](sql-database-import.md)
* [Импорт BACPAC-файла в базу данных SQL Azure с помощью PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Возможности базы данных SQL Azure](sql-database-features.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


