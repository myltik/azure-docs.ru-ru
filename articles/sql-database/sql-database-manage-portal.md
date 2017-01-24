---
title: "Управление базой данных SQL Azure с помощью портала Azure | Документация Майкрософт"
description: "Краткий справочник по управлению реляционной базой данных в облаке с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.custom: how to
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 09/19/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: dde22adda13b88a6cd0f1d033c2730ddc3288050


---
# <a name="manage-azure-sql-databases-using-the-azure-portal"></a>Управление базами данных SQL Azure с помощью портала Azure
> [!div class="op_single_selector"]
> * [портале Azure](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

[Портал Azure](https://portal.azure.com/) позволяет создавать базы данных и серверы SQL Azure, выполнять их мониторинг, а также управлять ими. В этой статье представлено краткое описание самых распространенных задач, а также ссылки на дополнительные сведения.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Просмотр баз данных, серверов и пулов SQL Azure
Чтобы просмотреть доступные службы базы данных SQL, щелкните **Больше служб** и в поле поиска введите **SQL**.

![База данных SQL](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Как создать или просмотреть базы данных SQL Azure?
Чтобы открыть колонку **Базы данных SQL**, щелкните **Базы данных SQL** и выберите базу данных, с которой будете работать, или щелкните **Добавить**, чтобы создать базу данных SQL. Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md).

![базы данных SQL;](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Как создать или просмотреть серверы SQL Azure?
Чтобы открыть колонку **Серверы SQL**, щелкните **Серверы SQL** и выберите сервер, с которым будете работать, или щелкните **Добавить**, чтобы создать сервер SQL. Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md).

![Серверы SQL Server](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Как создать или просмотреть пул эластичных БД SQL?
Чтобы открыть колонку **Пулы эластичных БД SQL**, щелкните **Пулы эластичных БД SQL** и выберите пул, с которым будете работать, или щелкните **Добавить**, чтобы создать пул. Дополнительные сведения см. в статье [Создание пула эластичных баз данных на портале Azure](sql-database-elastic-pool-create-portal.md).

![пулы эластичных БД SQL](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>Как обновить или просмотреть параметры базы данных SQL?
Чтобы просмотреть или обновить параметры базы данных, выберите нужный параметр в колонке базы данных SQL.

![Параметры базы данных SQL](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Как найти полное имя сервера баз данных SQL?
Чтобы просмотреть имя сервера баз данных, щелкните **Обзор** в колонке **База данных SQL** и запишите имя сервера.

![Параметры базы данных SQL](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Как управлять правилами брандмауэра, чтобы контролировать доступ к серверу и базе данных SQL?
Чтобы просмотреть, создать или изменить правила брандмауэра, щелкните **Настройка брандмауэра для сервера** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).

![Правила брандмауэра](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Как изменить уровень обслуживания или производительности базы данных SQL?
Чтобы обновить уровень обслуживания или производительности базы данных SQL, щелкните **Ценовая категория (единицы DTU масштабирования)** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL](sql-database-scale-up.md).

![ценовые категории](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Как настроить аудит и обнаружение угроз для базы данных SQL?
Чтобы настроить аудит и обнаружение угроз для базы данных SQL, щелкните **Аудит и обнаружение угроз** в колонке **База данных SQL**. Дополнительные сведения см. в статьях [Приступая к работе с аудитом базы данных SQL](sql-database-auditing-get-started.md) и [Приступая к работе с системой обнаружения угроз базы данных SQL](sql-database-threat-detection-get-started.md).

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Как настроить динамическое маскирование данных для базы данных SQL?
Чтобы настроить динамическое маскирование данных базы данных SQL, щелкните **Динамическое маскирование данных** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Начало работы с динамическим маскированием данных в базе данных SQL (портал Azure)](sql-database-dynamic-data-masking-get-started.md).

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Как настроить прозрачное шифрование данных (TDE) для базы данных SQL?
Чтобы настроить прозрачное шифрование данных для базы данных SQL, щелкните **Прозрачное шифрование данных** в колонке **База данных SQL**. Дополнительные сведения см. в разделе [Включение TDE в базе данных с помощью портала](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Как просмотреть или изменить максимальный размер базы данных SQL?
Чтобы просмотреть или изменить размер базы данных SQL, щелкните **Размер базы данных** в колонке **База данных SQL**. Чтобы обновить максимальный размер базы данных, измените уровень обслуживания или производительности. Дополнительные сведения см. в статье [Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Как выполнять мониторинг производительности базы данных SQL и повысить ее?
Чтобы выполнять мониторинг производительности базы данных SQL и повысить ее, щелкните **Обзор производительности** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Анализ производительности базы данных SQL](sql-database-performance.md).

## <a name="how-do-i-configure-geo-replication"></a>Как настроить георепликацию?
Чтобы настроить георепликацию для базы данных SQL, щелкните **Георепликация** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Настройка георепликации для базы данных SQL Azure с помощью портала Azure](sql-database-geo-replication-portal.md).

## <a name="how-do-i-fail-over-to-a-geo-replicated-sql-database"></a>Как выполнить отработку отказа и перейти в геореплицированную базу данных SQL?
Чтобы выполнить отработку отказа и перейти в геореплицированную базу данных-получатель, щелкните **Георепликация** в колонке **База данных SQL**, а затем щелкните **Отработка отказа**. Дополнительные сведения см. в статье [Запуск плановой или незапланированной отработки отказа для базы данных SQL Azure с помощью портала Azure](sql-database-geo-replication-failover-portal.md).

## <a name="how-do-i-copy-a-sql-database"></a>Как скопировать базу данных SQL?
Чтобы скопировать базу данных SQL, щелкните **Копировать** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Копирование базы данных SQL Azure с помощью портала Azure](sql-database-copy-portal.md).

![Параметры базы данных SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Как архивировать базу данных SQL Azure в BACPAC-файл?
Чтобы создать BACPAC-файл из базы данных SQL, щелкните **Экспортировать** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Архивация базы данных SQL Azure в BACPAC-файл с помощью портала Azure](sql-database-export.md).

![экспорт базы данных SQL](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Как восстановить состояние базы данных SQL до момента времени в прошлом?
Чтобы восстановить базу данных SQL, щелкните **Восстановить** в колонке **База данных SQL**. Дополнительные сведения см. в статье [Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure](sql-database-point-in-time-restore-portal.md).

![Параметры базы данных SQL](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Как создать базу данных SQL Azure из BACPAC-файла?
Чтобы создать базу данных SQL из BACPAC-файла, щелкните **Импорт базы данных** в колонке **Сервер SQL**. Дополнительные сведения см. в статье [Импорт BACPAC-файла для создания базы данных SQL Azure](sql-database-import.md).

![SQL Server](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>Как восстановить удаленную базу данных SQL?
Чтобы восстановить удаленную базу данных SQL, щелкните **Удаленные базы данных** в колонке **Сервер SQL** (сервер SQL, содержащий удаленную базу данных). Дополнительные сведения см. в статье [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Как удалить базу данных SQL?
Чтобы удалить базу данных SQL, щелкните **Удалить** в колонке **База данных SQL**. 

![Параметры базы данных SQL](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [База данных SQL](sql-database-technical-overview.md)
* [Мониторинг пула эластичных баз данных и управление им на портале Azure](sql-database-elastic-pool-manage-portal.md)




<!--HONumber=Dec16_HO1-->


