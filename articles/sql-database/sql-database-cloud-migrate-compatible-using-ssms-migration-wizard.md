---
title: "SSMS. Перенос базы данных SQL Server в базу данных SQL Azure | Документация Майкрософт"
description: "В этой статье показано, как использовать мастер развертывания базы данных в базу данных Microsoft Azure в среде SQL Server Management Studio для переноса совместимой базы данных SQL Server непосредственно на сервер базы данных SQL Azure."
keywords: "База данных SQL Microsoft Azure, миграция базы данных, мастер баз данных Microsoft Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 91d3fd5e-d035-4c55-b1ea-a7ccc8e0f543
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f4cbfc1a277cbe80edbfbb4dc651adb1972f41b4


---
# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Миграция базы данных SQL Server в Базу данных SQL с помощью мастера развертывания базы данных в Базу данных Microsoft Azure
> [!div class="op_single_selector"]
> * [Мастер миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Экспорт в BACPAC-файл](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Импорт из BACPAC-файла](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

В этой статье показано, как использовать мастер развертывания базы данных в базу данных Microsoft Azure в среде SQL Server Management Studio для переноса [совместимой базы данных SQL Server](sql-database-cloud-migrate.md) непосредственно на сервер базы данных SQL Azure.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Использование мастера развертывания базы данных в Базу данных Microsoft Azure
> [!NOTE]
> В описанных ниже действиях предполагается, что у вас есть [подготовленный сервер Базы данных SQL](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).
> 
> 

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.
   
   > [!IMPORTANT]
   > Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Откройте среду Management Studio и подключитесь к базе данных SQL Server, которую нужно перенести, в обозревателе объектов.
3. В обозревателе объектов щелкните базу данных правой кнопкой мыши, наведите указатель мыши на пункт **Задачи** и выберите команду **Развертывание базы данных в Базе данных SQL Microsoft Azure…**
   
    ![Развертывание в Azure из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)
4. В мастере развертывания щелкните **Далее** и **Подключиться**, чтобы настроить подключение к серверу базы данных SQL.
   
   ![Развертывание в Azure из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)
5. В диалоговом окне "Подключение к серверу" введите сведения о подключении, чтобы подключиться к серверу Базы данных SQL. Чтобы получить имя имеющегося сервера, см. сведения в статье [Просмотр и обновление сервера базы данных SQL Azure и его параметров](sql-database-view-update-server-settings.md).
   
    ![Развертывание в Azure из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)
6. Предоставьте следующие данные для [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -файла, создаваемого этим мастером при переносе:
   
   * **имя новой базы данных** 
   * **выпуск Базы данных SQL Microsoft Azure** ([уровень служб](sql-database-service-tiers.md));
   * **максимальный размер базы данных**
   * **цель обслуживания** (уровень производительности);
   * **имя временного файла**  
   
   ![Параметры экспорта](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)
7. Завершите работу мастера. В зависимости от размера и сложности базы данных процесс развертывания может занять от нескольких минут до нескольких часов. Если мастер выявляет проблемы совместимости, ошибки отображаются на экране и перенос останавливается. Руководство по устранению проблем совместимости базы данных см. в статье [Устранение проблем совместимости базы данных SQL Server с помощью мастера миграции SQL Azure перед миграцией в базу данных SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md).
8. В обозревателе объектов подключитесь к перенесенной базе данных на сервере базы данных SQL Azure.
9. На портале Azure просмотрите базу данных и ее свойства.

## <a name="next-steps"></a>Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Возможности базы данных SQL Azure](sql-database-features.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


