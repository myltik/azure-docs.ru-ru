---
title: "SSMS. Экспорт базы данных в BACPAC-файл (Azure) | Документация Майкрософт"
description: "В этой статье показано, как экспортировать базу данных SQL Server в BACPAC-файл, используя мастер экспорта приложений на уровне данных в среде SQL Server Management Studio."
keywords: "База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, мастер экспорта приложений на уровне данных "
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
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Экспорт базы данных SQL Azure или SQL Server в BACPAC-файл с помощью SQL Server Management Studio        
        
В этой статье показано, как экспортировать базу данных SQL Azure или SQL Server в [BACPAC-файл](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4), используя мастер экспорта приложений на уровне данных в среде SQL Server Management Studio. Общие сведения об экспорте в BACPAC-файл см. в [этой статье](sql-database-export.md).    
        
1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.        
           
   > [!IMPORTANT]        
   > Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Последняя версия SSMS](https://msdn.microsoft.com/library/mt238290.aspx).        
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
* [Последняя версия SSMS](https://msdn.microsoft.com/library/mt238290.aspx)
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Server, ознакомьтесь с разделом [Импорт файла BACPAC для создания новой пользовательской базы данных](https://msdn.microsoft.com/library/hh710052.aspx)
* Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-cloud-migrate.md).
* Дополнительные сведения о длительном периоде удержания резервных копий базы данных SQL Azure как альтернативе экспорту базы данных для создания архива см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).


    



<!--HONumber=Feb17_HO2-->


