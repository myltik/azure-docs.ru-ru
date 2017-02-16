---
title: "SSMS. Совместимость базы данных SQL Server Azure | Документация Майкрософт"
description: "В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для миграции с помощью мастера экспорта приложений на уровне данных в среде SQL Server Management Studio."
keywords: "База данных SQL Microsoft Azure, миграция базы данных, совместимость базы данных SQL, мастер экспорта приложений на уровне данных "
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 82b76602133fa42db14e2095f29535b6002e9b2f
ms.openlocfilehash: efcf1179f744e1b3d1645321af62d2533677afd2


---
# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Использование SQL Server Management Studio для определения совместимости базы данных SQL Azure перед миграцией
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для миграции с помощью мастера экспорта приложений на уровне данных в среде SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Использование среды SQL Server Management Studio
1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.
   
   > [!IMPORTANT]
   > Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Откройте среду Management Studio и подключитесь к исходной базе данных в обозревателе объектов.
3. В обозревателе объектов щелкните правой кнопкой мыши исходную базу данных, наведите указатель мыши на пункт **Задачи** и выберите команду **Экспорт приложения уровня данных...**
   
    ![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. В окне мастера экспорта щелкните **Далее** и на вкладке **Параметры** настройте сохранение BACPAC-файла на локальный диск или в большой двоичный объект Azure. BACPAC-файл будет сохранен, если нет проблем совместимости базы данных. При наличии проблем совместимости они отображаются в консоли.
   
    ![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)
5. Чтобы пропустить экспорт данных, перейдите на вкладку **Дополнительно** и снимите флажок **Выделить все**. На этом этапе нашей целью является только проверка совместимости.
   
    ![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)
6. Нажмите кнопку **Далее**, а затем — кнопку **Готово**. При наличии проблем совместимости базы данных они отобразятся после проверки схемы мастером.
   
    ![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)
7. Если проблемы отсутствуют, значит база данных совместима и все готово к переносу. Если имеются проблемы, их необходимо устранить. Чтобы просмотреть проблемы, щелкните ссылку **Ошибка** для соответствующей **проверки схемы**. 
    ![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)
8. Если файл *. BACPAC успешно создан, то ваша база данных совместима с Базой данных SQL и вы готовы к миграции.

## <a name="next-steps"></a>Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Устранение проблем совместимости при миграции базы данных](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Возможности базы данных SQL Azure](sql-database-features.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO2-->


