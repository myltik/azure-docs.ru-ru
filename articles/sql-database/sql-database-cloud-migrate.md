<properties
   pageTitle="Миграция базы данных SQL Server в базу данных SQL | Microsoft Azure"
   description="Узнайте, как выполнить миграцию локальной базы данных SQL Server в базу данных SQL Azure в облаке. Используйте средства миграции баз данных для проверки совместимости перед миграцией базы данных."
   keywords="миграция базы данных, миграция базы данных SQL Server, средства миграции базы данных, миграция базы данных, миграция базы данных SQL"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Миграция базы данных SQL Server в базу данных SQL в облаке

Из этой статьи вы узнаете, как перенести локальную базу данных SQL Server 2005 или более поздней версии в Базу данных SQL Azure. В процессе миграции базы данных выполняется перенос схемы и данных из базы данных SQL Server в текущей среде в базу данных SQL при условии, что существующая база данных удовлетворяет критериям совместимости. Начиная с [Базы данных SQL версии 12](sql-database-v12-whats-new.md), осталось очень мало проблем совместимости, не связанных с операциями уровня сервера и операциями между базами данных. Базы данных и приложения, опирающиеся на [частично или полностью неподдерживаемые функции](sql-database-transact-sql-information.md), потребуют небольшой доработки для [устранения этих несовместимостей](sql-database-cloud-migrate-fix-compatibility-issues.md) перед миграцией базы данных SQL Server.

> [AZURE.NOTE] Информацию о переносе в Базу данных SQL Azure баз данных, отличных от SQL Server, в том числе Microsoft Access, Sybase, MySQL Oracle и DB2, см. в статье [Помощник по миграции SQL Server](http://blogs.msdn.com/b/ssma/).

## Средства миграции баз данных проверяют совместимость базы данных SQL Server с базой данных SQL.

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Чтобы проверить отсутствие проблем совместимости базы данных SQL перед началом процесса миграции, используйте один из следующих методов.

- [С помощью SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md). SqlPackage — это программа командной строки, которая проверит наличие проблем совместимости и, если они будут найдены, составит отчет с их описанием.
- [С помощью SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md). Мастер экспорта данных уровня приложения в среде SQL Server Management Studio отобразит обнаруженные ошибки на экране.

## Устранение проблем совместимости при миграции базы данных

При обнаружении проблем совместимости необходимо устранить их перед выполнением миграции базы данных SQL Server. Используйте следующие средства миграции базы данных:

- С помощью [мастера миграции SQL Azure (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md)
- С помощью [SQL Server Data Tools для Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- С помощью среды [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Перенос совместимой базы данных SQL Server в Базу данных SQL

Корпорация Майкрософт предоставляет несколько методов переноса совместимой базы данных SQL Server для различных сценариев. Выбор способа зависит от допустимости простоев, размера и сложности базы данных SQL Server и вашего подключения к облаку Microsoft Azure.

> [AZURE.SELECTOR]
- [Мастер миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Экспорт в BACPAC-файл](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Импорт из BACPAC-файла](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Первый вопрос, который нужно задать для выбора метода переноса, — можно ли вывести базу данных из рабочего режима на время переноса. Миграция базы данных при наличии активных транзакций может привести к несогласованности баз данных и повреждению базы данных. Существует множество методов замораживания базы данных — от запрета подключения клиента до создания [моментального снимка базы данных](https://msdn.microsoft.com/library/ms175876.aspx).

Для переноса с минимальным временем простоя используйте [репликацию транзакций SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md), если база данных соответствует требованиям для репликации транзакций. Если вы можете позволить себе некоторое время простоя или выполняете тестовую миграцию рабочей базы данных для последующей полноценной миграции, воспользуйтесь одним из следующих трех методов.

- [Мастер миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md). Для переноса малых и средних совместимых баз данных SQL Server 2005 или более поздних версий достаточно запустить [мастер по развертыванию базы данных в Базе данных Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) в среде SQL Server Management Studio.
- [Экспорт в файл BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) и последующий [Импорт из файла BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md). При наличии проблем подключения (отсутствие подключения, низкая пропускная способность или проблемы с тайм-аутом), а также для средних и больших баз данных используйте файл [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). При выборе этого метода схема и данные базы данных SQL Server экспортируются в файл BACPAC, а затем файл BACPAC импортируется в Базу данных SQL с использованием мастера экспорта приложений уровня данных в SQL Server Management Studio или программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Совместное использование BACPAC и BCP. Используйте файл [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) и [BCP](https://msdn.microsoft.com/library/ms162802.aspx) для больших баз данных для достижения более высокого уровня параллельной обработки с целью увеличения производительности, пусть и за счет увеличения сложности. С помощью этого метода выполните перенос схемы и данных отдельно.
 - [Экспортируйте только схему в файл BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Импортируйте только схему из файла BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) в Базу данных SQL.
 - Используйте [BCP](https://msdn.microsoft.com/library/ms162802.aspx) для извлечения данных в плоские файлы и затем выполните [параллельную загрузку](https://technet.microsoft.com/library/dd425070.aspx) этих файлов в Базу данных SQL Azure.

	 ![Миграция базы данных SQL Server — миграция базы данных SQL в облако](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0316_2016-->