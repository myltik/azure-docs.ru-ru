<properties
   pageTitle="Перенос данных в базу данных SQL с использованием транзакционной репликации | Microsoft Azure"
   description="База данных SQL Microsoft Azure, миграция базы данных, импорт базы данных, транзакционная репликация"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Миграция базы данных SQL Server в базу данных SQL Azure с использованием репликации транзакций

> [AZURE.SELECTOR]
- [Мастер миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Экспорт в BACPAC-файл](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Импорт из BACPAC-файла](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

В этой статье вы узнаете, как перенести совместимую базу данных SQL Server в базу данных SQL Azure с минимальным временем простоя, используя репликацию транзакций SQL Server.

## Основные сведения об архитектуре репликации транзакций

Если вы не можете отключить базу данных SQL Server от рабочих процессов на время миграции, вы можете воспользоваться транзакционной репликацией SQL Server в качестве решения для миграции. Чтобы использовать это решение, следует настроить базу данных SQL Azure в качестве подписчика на локальный экземпляр SQL Server, который требуется перенести. Локальной распространитель репликации транзакций обеспечит синхронизацию данных из локальной базы данных (издателя) по мере возникновения новых транзакций.

Транзакционную репликацию также можно использовать для переноса части вашей локальной базы данных. Публикации, которые вы реплицируете в Базу данных SQL Azure, могут быть ограничены подмножеством таблиц в реплицируемой базе данных. Кроме того, для каждой реплицируемой таблицы вы можете ограничить данные подмножеством строк или подмножеством столбцов.

При транзакционной репликации все изменения данных или схемы, происходящие в промежутке между моментом запуска миграции и моментом ее завершения отобразятся в Базе данных SQL Azure.

После завершения синхронизации и подготовки к миграции необходимо просто изменить строку подключения ваших приложений, чтобы направить их в базу данных SQL Azure, а не в локальную базу данных. После того как транзакционная репликация применила все изменения из вашей локальной базы данных и все приложения направлены к Базе данных Azure, вы можете безопасно удалить репликацию и оставить Базу данных SQL Azure в качестве производственной системы.

 ![Диаграмма SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## Требования к репликации транзакций

Транзакционная репликация — это технология, встроенная и интегрированная с SQL Server, начиная с версии SQL Server 6.5. Это надежная и проверенная технология, известная большинству администраторов баз данных. В [предварительной версии SQL Server 2016](http://www.microsoft.com/server-cloud/products/sql-server-2016/) теперь можно настроить Базу данных SQL Azure в виде [подписчика транзакционной репликации](https://msdn.microsoft.com/library/mt589530.aspx) на ваши локальные публикации. Процесс настройки из среды Management Studio точно такой же, как и процесс настройки подписчика транзакционной репликации на локальном сервере. Такой сценарий поддерживается, если в качестве издателя и распространителя используются как минимум следующие версии SQL Server:

 - SQL Server 2016 CTP3 (предварительная версия) и выше 
 - SQL Server 2014 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3) и выше
 - SQL Server 2014 RTM с накопительным пакетом обновления 10 (CU10) и выше
 - SQL Server 2012 с пакетом обновления 2 (SP2) и накопительным пакетом обновления 8 (CU8) и выше
 - SQL Server 2013 с пакетом обновления 3 (SP3) после выхода


> [AZURE.IMPORTANT] Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и базой данных SQL, необходимо всегда использовать последнюю версию Management Studio. Более старые версии SQL Server Management Studio не позволяют настроить базу данных SQL как подписчика. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Дальнейшие действия

- [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016 (предварительная версия)](http://www.microsoft.com/server-cloud/products/sql-server-2016/)

## Дополнительные ресурсы

- [Репликация транзакций](https://msdn.microsoft.com/library/mt589530.aspx)
- [База данных SQL версии 12](sql-database-v12-whats-new.md).
- [Частично или полностью неподдерживаемые функции Transact-SQL](sql-database-transact-sql-information.md).
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->