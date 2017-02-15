---
title: "Перенос базы данных SQL Server в базу данных SQL Azure с использованием репликации транзакций | Документация Майкрософт"
description: "База данных SQL Microsoft Azure, миграция базы данных, импорт базы данных, транзакционная репликация"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Миграция базы данных SQL Server в базу данных SQL Azure с использованием репликации транзакций
> [!div class="op_single_selector"]
> * [Мастер миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Экспорт в BACPAC-файл](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Импорт из BACPAC-файла](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

В этой статье вы узнаете, как перенести совместимую базу данных SQL Server в базу данных SQL Azure с минимальным временем простоя, используя репликацию транзакций SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Основные сведения об архитектуре репликации транзакций
Если вы не можете отключить базу данных SQL Server от рабочих процессов на время миграции, вы можете воспользоваться транзакционной репликацией SQL Server в качестве решения для миграции. Чтобы использовать это решение, следует настроить базу данных SQL Azure в качестве подписчика на локальный экземпляр SQL Server, который требуется перенести. Локальной распространитель репликации транзакций обеспечивает синхронизацию данных из локальной базы данных (издателя) по мере возникновения новых транзакций. 

Транзакционную репликацию также можно использовать для переноса части вашей локальной базы данных. Публикации, которые вы реплицируете в Базу данных SQL Azure, могут быть ограничены подмножеством таблиц в реплицируемой базе данных. Для каждой реплицируемой таблицы вы можете ограничить данные подмножеством строк или подмножеством столбцов.

При транзакционной репликации все изменения данных или схемы отображаются в Базе данных SQL Azure. После завершения синхронизации и подготовки к миграции измените строку подключения ваших приложений, чтобы направить их в Базу данных SQL Azure. После того как транзакционная репликация применила все изменения из вашей локальной базы данных и все приложения направлены к базе данных Azure, вы можете удалить транзакционную репликацию. База данных SQL Azure теперь является рабочей системой.

 ![Диаграмма SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="how-transactional-replication-works"></a>Как действует репликация транзакций

Репликация транзакций включает в себя три 3 основных составляющих. Это издатель, распространитель и подписчик. Вместе эти составляющие выполняют репликацию. Распространитель отвечает за контроль процессов, которые перемещают данные между серверами. При настройке распространения SQL создаст базу данных распространителя. Каждый издатель должен быть привязан к базе данных распространителя. База данных распространителя содержит метаданные для каждой связанной публикации и данные о ходе выполнения каждой репликации. Для репликации транзакций она будет содержать все транзакции, которые должны быть выполнены в подписчике.

Издатель — это база данных, из которой поступают все данные для переноса. В издателе может быть множество публикаций. Эти публикации содержат статьи, сопоставляемые со всеми таблицами и данными, которые необходимо реплицировать. В зависимости от способа определения публикации и статей можно реплицировать всю базу данных или ее часть. 

В процессе репликации подписчик — это сервер, который получает все данные и транзакции из публикации. У каждой публикации может быть несколько репликаций.

## <a name="transactional-replication-requirements"></a>Требования к репликации транзакций
[Перейдите по следующей ссылке, чтобы просмотреть обновленный список требований.](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базы данных SQL, всегда используйте последнюю версию SQL Server Management Studio. Более старые версии SQL Server Management Studio не позволяют настроить Базу данных SQL как подписчика. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Рабочий процесс миграции в базу данных SQL с использованием репликации транзакций

1. Настройка распространения
   -  [С помощью SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1).
   -  [С помощью Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2).
2. Создание публикации
   -  [С помощью SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1).
   -  [С помощью Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2).
3. Создание подписки
   -  [С помощью SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0).
   -  [С помощью Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1).

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Некоторые советы и описание различий при миграции в базу данных SQL

1. Использование локального распространителя 
   - Это отрицательно повлияет на производительность сервера. 
   - Если снижение производительности неприемлемо, вы можете использовать другой сервер, но это усложнит управление и администрирование.
2. При выборе папки моментальных снимков убедитесь, что ее емкость достаточна для хранения BCP каждой таблицы, которую требуется реплицировать. 
3. Обратите внимание, что при создании моментального снимка связанные таблицы будут блокироваться до завершения этой операции. Помните об этом при планировании создания моментальных снимков. 
4. В базе данных SQL Azure поддерживаются принудительные подписки:
   - Можно добавлять только подписчики, расположенные на стороне вашей локальной базы данных.

## <a name="next-steps"></a>Дальнейшие действия
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Дополнительные ресурсы
* Дополнительные сведения о репликации транзакций см. в разделе [Репликация базы данных SQL](https://msdn.microsoft.com/library/mt589530.aspx).
* Чтобы изучить общий процесс миграции и доступные варианты, ознакомьтесь с разделом [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).



<!--HONumber=Dec16_HO2-->


