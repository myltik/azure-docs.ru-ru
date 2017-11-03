---
title: "Перенос базы данных SQL Server в базу данных SQL Azure | Документация Майкрософт"
description: "Сведения о том, как выполнить миграцию базы данных SQL Server в базу данных SQL Azure в облаке."
keywords: "миграция базы данных, миграция базы данных SQL Server, средства миграции базы данных, миграция базы данных, миграция базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 02/08/2017
ms.author: carlrab
ms.openlocfilehash: f27d2fbeb8ec514419bd0d208429e3d3de2d07ea
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Миграция базы данных SQL Server в базу данных SQL в облаке
В этой статье вы узнаете о двух основных методах миграции базы данных SQL Server 2005 или более поздней версии в базу данных SQL Azure. Первый метод проще, но миграция происходит с простоем, который может длиться достаточно долго. Второй метод более сложен, но значительно сокращает время простоя при выполнении миграции.

В обоих случаях необходимо обеспечить совместимость базы данных-источника с базой данных SQL Azure с помощью [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Начиная с базы данных SQL версии 12, у всех последующих версий базы данных SQL Server функции, не связанные с операциями уровня сервера и операциями между базами данных, [практически не отличаются](sql-database-features.md). Базы данных и приложения, использующие [частично поддерживаемые или неподдерживаемые функции](sql-database-transact-sql-information.md), требуют небольшой [доработки для устранения этих несовместимостей](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) перед миграцией базы данных SQL Server.

> [!NOTE]
> Сведения о переносе в базу данных SQL Azure баз данных, отличных от SQL Server, в том числе Microsoft Access, Sybase, MySQL Oracle и DB2, см. в блоге, посвященном [помощнику по миграции SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/12/22/released-sql-server-migration-assistant-ssma-v7-2/).
> 

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Метод 1. Миграция с простоем

 Используйте этот метод, если вы можете позволить себе некоторое время простоя или выполняете тестовую миграцию рабочей базы данных для последующей полноценной миграции. Руководство см. в статье [Migrate a SQL Server database](sql-database-migrate-your-sql-server-database.md) (Миграция базы данных SQL Server).

Ниже представлен общий рабочий процесс миграции базы данных SQL Server с помощью этого метода.

  ![Схема переноса VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. Оцените базу данных для обеспечения совместимости с помощью [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) последней версии.
2. Подготовьте все необходимые исправления в виде скриптов Transact-SQL.
3. Создайте транзакционно согласованную копию базы данных-источника для миграции. В нее не должны вноситься дальнейшие изменения (такие изменения можно применить вручную после завершения миграции). Существует множество методов замораживания базы данных — от запрета подключения клиента до создания [моментального снимка базы данных](https://msdn.microsoft.com/library/ms175876.aspx).
4. Разверните скрипты Transact-SQL для применения исправлений к копии базы данных.
5. [Экспортируйте](sql-database-export.md) копию базы данных в BACPAC-файл на локальном диске.
6. [Импортируйте](sql-database-import.md) BACPAC-файл как новую базу данных SQL Azure, используя некоторые средства импорта BACPAC, в том числе SQLPackage.exe, который является рекомендуемым средством для повышения производительности.

### <a name="optimizing-data-transfer-performance-during-migration"></a>Оптимизация производительности передачи данных во время миграции 

Ниже представлены рекомендации по оптимизации производительности во время импорта.

* Выберите наивысший уровень служб и производительности, который позволяет ваш бюджет, чтобы увеличить производительность передачи данных. Чтобы сэкономить деньги, вы можете уменьшить производительность после завершения миграции. 
* Минимизируйте расстояние между BACPAC-файлом и целевым центром обработки данных.
* Отключите автоматическую статистику во время миграции.
* Выполните секционирование таблиц и индексов.
* Удалите индексированные представления и заново создайте их по завершении миграции.
* Скопируйте редко запрашиваемые данные журнала в другую базу данных, а затем перенесите их в отдельную базу данных SQL Azure. Затем вы сможете запросить эти данные с помощью [эластичных запросов](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Оптимизация производительности после завершения миграции

[Обновите статистику](https://msdn.microsoft.com/library/ms187348.aspx) с использованием полной проверки после завершения миграции.

## <a name="method-2-use-transactional-replication"></a>Метод 2. Использование репликации транзакций

Если вы не можете отключить базу данных SQL Server от рабочих процессов на время миграции, вы можете воспользоваться транзакционной репликацией SQL Server в качестве решения для миграции. Чтобы использовать этот метод, база данных-источник должна соответствовать [требованиям к репликации транзакций](https://msdn.microsoft.com/library/mt589530.aspx) и быть совместимой с базой данных SQL Azure. См. дополнительные сведения о [настройке репликации для групп доступности AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Чтобы использовать это решение, следует настроить базу данных SQL Azure в качестве подписчика на экземпляр SQL Server, который требуется перенести. Распространитель репликации транзакций обеспечивает синхронизацию данных из базы данных (издателя) по мере возникновения новых транзакций. 

При транзакционной репликации все изменения данных или схемы отображаются в Базе данных SQL Azure. После завершения синхронизации и подготовки к миграции измените строку подключения ваших приложений, чтобы направить их в Базу данных SQL Azure. После того как репликация транзакций применила все изменения из вашей базы данных-источника и все приложения направлены к базе данных Azure, вы можете удалить репликацию транзакций. База данных SQL Azure теперь является рабочей системой.

 ![Диаграмма SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Репликацию транзакций также можно использовать для миграции части вашей базы данных-источника. Публикации, которые вы реплицируете в Базу данных SQL Azure, могут быть ограничены подмножеством таблиц в реплицируемой базе данных. Для каждой реплицируемой таблицы вы можете ограничить данные подмножеством строк или подмножеством столбцов.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Рабочий процесс миграции в базу данных SQL с использованием репликации транзакций

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базы данных SQL, всегда используйте последнюю версию SQL Server Management Studio. Более старые версии SQL Server Management Studio не позволяют настроить Базу данных SQL как подписчика. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Настройка распространения
   -  [С помощью SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1).
   -  [С помощью Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2).
2. Создание публикации
   -  [С помощью SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1).
   -  [С помощью Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2).
3. Создание подписки
   -  [С помощью SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0).
   -  [С помощью Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1).

### <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Некоторые советы и описание различий при миграции в базу данных SQL

1. Использование локального распространителя 
   - Это отрицательно влияет на производительность сервера. 
   - Если снижение производительности неприемлемо, вы можете использовать другой сервер, но это усложнит управление и администрирование.
2. При выборе папки моментальных снимков убедитесь, что ее емкость достаточна для хранения BCP каждой таблицы, которую требуется реплицировать. 
3. Создание моментального снимка блокирует связанные таблицы до завершения операции. Учитывайте это при планировании создания моментальных снимков. 
4. В базе данных SQL Azure поддерживаются только принудительные подписки. Вы можете добавлять только подписчики из базы данных-источника.

## <a name="resolving-database-migration-compatibility-issues"></a>Устранение проблем совместимости при миграции базы данных
Существует широкий спектр проблем совместимости, которые могут возникнуть в зависимости от версии исходной базы данных SQL Server и сложности базы данных, для которой выполняется миграция. В более старых версиях SQL Server имеются дополнительные проблемы совместимости. Воспользуйтесь поиском в Интернете, а также следующими ресурсами:

* [Функции базы данных SQL Server, которые не поддерживаются в базе данных SQL Azure](sql-database-transact-sql-information.md)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Неподдерживаемые функции ядра СУБД в SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Помимо поиска в Интернете и использования этих ресурсов можно воспользоваться [форумами сообщества SQL Server на сайте MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) или [StackOverflow](http://stackoverflow.com/).

## <a name="next-steps"></a>Дальнейшие действия
* Воспользуйтесь скриптом в блоге разработчиков EMEA SQL Azure для [отслеживания использования базы данных TempDB во время миграции](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Воспользуйтесь скриптом в блоге разработчиков EMEA SQL Azure, чтобы [отслеживать объем, занимаемый журналом транзакций в базе данных, во время миграции](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Сведения об изменении часового пояса по умолчанию для локального часового пояса см. на [этой странице](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
* Сведения об изменении языка по умолчанию в базе данных SQL Azure после миграции см. на [этой странице](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).


