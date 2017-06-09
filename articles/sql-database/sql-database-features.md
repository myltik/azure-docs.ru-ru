---
title: "Обзор возможностей базы данных SQL Azure | Документация Майкрософт"
description: "На этой странице представлен обзор логических серверов и баз данных SQL Azure, а также таблица со сведениями о поддержке функций и ссылки для каждой из них."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Возможности базы данных SQL Azure

В следующих таблицах перечислены основные функции базы данных SQL Azure и аналогичные функции SQL Server, а также сведения о поддержке каждой конкретной функции и ссылки на дополнительные сведения о функции на каждой платформе. Сведения об отличиях Transact-SQL, которые следует учитывать при миграции имеющегося решения базы данных, см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).

Мы продолжаем расширять возможности базы данных SQL Azure. Поэтому рекомендуем вам почаще посещать нашу веб-страницу с обновлениями служб Azure и при работе с ней пользоваться фильтрами.

* Фильтруйте обновления по [службе базы данных SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Фильтруйте обновления по [объявлениям об общедоступных версиях](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) функций базы данных SQL.

| **Компонент** | **SQL Server** | **База данных SQL Azure;** | 
| --- | :---: | :---: | 
| Активная георепликация | Не поддерживается. Дополнительные сведения см. в статье [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) (Группы доступности AlwaysOn). | [Поддерживаются](sql-database-geo-replication-overview.md)
| Постоянное шифрование | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Поддерживается. Дополнительные сведения см. в статье [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище сертификатов Windows](sql-database-always-encrypted.md) и [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md).|
| Группы доступности AlwaysOn | [Поддерживаются](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Не поддерживается. Ознакомьтесь со статьей [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md). |
| Присоединение базы данных | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Не поддерживается |
| Роли приложений | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Автомасштабирование | Не поддерживается | Поддерживается. Дополнительные сведения см. в статье [Параметры базы данных SQL и производительность: возможности разных уровней служб](sql-database-service-tiers.md). |
| Azure Active Directory | Не поддерживается | [Поддерживаются](sql-database-aad-authentication.md) |
| Фабрика данных Azure | [Поддерживаются](../data-factory/data-factory-introduction.md) | [Поддерживаются](../data-factory/data-factory-introduction.md) |
| Аудит | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Поддерживаются](sql-database-auditing.md) |
| BACPAC-файл (экспорт) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Поддерживаются](sql-database-export.md) |
| BACPAC-файл (импорт) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Поддерживаются](sql-database-import.md) |
| АРХИВАЦИЯ | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Не поддерживается |
| Встроенные функции | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/functions/functions) | Большинство. Дополнительные сведения см. в разделах по [отдельным функциям] (https://docs.microsoft.com/sql/t-sql/functions/functions). |
| Запись измененных данных | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Не поддерживается |
| Отслеживание изменений | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Инструкции параметров сортировки | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Индексы columnstore | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Только в Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| Среда CLR | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Не поддерживается |
| автономные базы данных; | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Автономные пользователи | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Поддерживаются](sql-database-manage-logins.md#non-administrator-users) |
| Ключевые слова языка управления потоком | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Запросы баз данных | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Частично. Дополнительные сведения см. в статье [Обзор эластичных запросов к базе данных SQL Azure (предварительная версия)](sql-database-elastic-query-overview.md). |
| Курсоры | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Сжатие данных | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Резервные копии базы данных | [Управление осуществляет пользователь](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Управление осуществляет служба базы данных SQL Azure](sql-database-automated-backups.md) |
| Компонент Database Mail | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Не поддерживается |
| Зеркальное отображение базы данных | [Поддерживаются](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Не поддерживается |
| Параметры конфигурации базы данных | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Поддерживаются](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Не поддерживается |
| Моментальные снимки базы данных | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Не поддерживается |
| Типы данных | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| Инструкции DBCC | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Большинство. Дополнительные сведения см. в разделах по [отдельным инструкциям](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql). |
| Инструкции языка DDL | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/statements) | Большинство. Дополнительные сведения см. в разделах по [отдельным инструкциям](https://docs.microsoft.com/sql/t-sql/statements/statements).
| Триггеры DDL | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Только база данных](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Распределенные транзакции | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Только сценарии базы данных SQL |
| Инструкции языка DML | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/queries/queries) | Большинство. Дополнительные сведения см. в разделах по [отдельным инструкциям] (https://docs.microsoft.com/sql/t-sql/queries/queries). |
| Триггеры DML | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [Все](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Некоторые. Дополнительные сведения см. в разделах по [отдельным динамическим административным представлениям](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). |
| Пулы эластичных БД | Не поддерживается | [Поддерживаются](sql-database-elastic-pool.md) |
| Задания обработки эластичных БД | Не поддерживаются. Ознакомьтесь со статьей [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent). | [Поддерживаются](sql-database-elastic-jobs-getting-started.md) | 
| Эластичные запросы | Не поддерживаются. Ознакомьтесь со статьей [Межбазовые запросы](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries). | [Поддерживаются](sql-database-elastic-query-overview.md) |
| Уведомления о событиях | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Поддерживаются](sql-database-insights-alerts-portal.md) |
| Выражения | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Расширенные события | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Некоторые. Дополнительные сведения см. в разделах по [отдельным событиям](sql-database-xevent-db-diff-from-svr.md). |
| Расширенные хранимые процедуры | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Не поддерживается |
| Файлы и группы файлов | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Только первичные](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Файловый поток | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Не поддерживается |
| полнотекстовый поиск. | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [Не поддерживаются сторонние средства разбиения текста на слова](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Функции | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/functions/functions) | Большинство. Дополнительные сведения см. в разделах по [отдельным функциям](https://docs.microsoft.com/sql/t-sql/functions/functions). |
| Оптимизация в памяти | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Только в Premium Edition](sql-database-in-memory.md) |
| Задания | Дополнительные сведения см. в статье [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) (Агент SQL Server) | Дополнительные сведения см. в статье [Начало работы с заданиями обработки эластичных баз данных](sql-database-elastic-jobs-getting-started.md) |
| Поддержка данных JSON | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Поддерживаются](sql-database-json-features.md) |
| Элементы языка | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Большинство. Дополнительные сведения см. в разделах по [отдельным элементам](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql). |  
| Связанные службы | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Не поддерживаются. Ознакомьтесь со статьей об [эластичных запросах](sql-database-elastic-query-horizontal-partitioning.md). |
| Доставка журналов | [Поддерживаются](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Не поддерживается. Ознакомьтесь со статьей [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md). |
| Master Data Services (MDS) | [Поддерживаются](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Не поддерживается |
| Минимальное ведение журнала при массовом импорте | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Не поддерживается |
| Изменение системных данных | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Не поддерживается |
| Операции с индексом в сети | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Поддерживается. Размер транзакции ограничен уровнем службы.](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Операторы | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Большинство. Дополнительные сведения см. в разделах по [отдельным операторам](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql). |
| Восстановление базы данных до точки во времени | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Поддерживаются](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Не поддерживается
| Управление на основе политик | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Не поддерживается |
| Предикаты | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Большинство. Дополнительные сведения см. в разделах по [отдельным предикатам](https://docs.microsoft.com/sql/t-sql/queries/predicates).
| Службы R | [Поддерживаются](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Регулятор ресурсов | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Не поддерживается |
| Инструкции RESTORE | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Не поддерживается | 
| Восстановление базы данных из резервной копии | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Только из встроенных резервных копий](sql-database-recovery-using-backups.md) |
| Безопасность на уровне строк | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Семантический поиск | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Не поддерживается |
| Порядковые номера | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [Поддерживаются](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Не поддерживается |
| Параметры конфигурации сервера | [Поддерживаются](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Не поддерживаются. Ознакомьтесь со статьей о [параметрах конфигурации базы данных](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql). |
| Инструкции SET | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Большинство. Дополнительные сведения см. в разделах по [отдельным инструкциям](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql). 
| пространственный индекс | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| Агент SQL Server | [Поддерживаются](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Не поддерживается. Ознакомьтесь со статьей о [заданиях обработки эластичных БД](sql-database-elastic-jobs-getting-started.md). |
| SQL Server Analysis Services (SSAS) | [Поддерживаются](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Не поддерживаются. Ознакомьтесь со страницей [Службы Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| SQL Server Integration Services (SSIS); | [Поддерживаются](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Не поддерживаются. Ознакомьтесь со страницей [Фабрика данных](https://azure.microsoft.com/services/data-factory/). |
| SQL Server PowerShell | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [Поддерживаются](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Не поддерживается. Ознакомьтесь со статьей о [расширенных событиях](sql-database-xevent-db-diff-from-svr.md). |
| Репликация SQL Server | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Только для подписчиков репликации транзакций и репликации моментального снимка](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Поддерживаются](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Не поддерживается |
| Хранимые процедуры | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Системные хранимые функции | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по [отдельным функциям](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql). |
| Системные хранимые процедуры | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по [отдельным хранимым процедурам](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql). |
| Системные таблицы | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по [отдельным таблицам](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql). |
| Представления системных каталогов | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по [отдельным представлениям](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql).
| Секционирование таблиц. | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Поддерживается. [Только первичная группа файлов](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes). |
| Временные таблицы | [Локальные и глобальные](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Только локальные](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Временные таблицы | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Поддерживаются](sql-database-temporal-tables.md) |
| Транзакции | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Переменные | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Прозрачное шифрование данных (TDE)  | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Поддерживаются](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Отказоустойчивый кластер Windows Server | [Поддерживаются](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Не поддерживается. Ознакомьтесь со статьей [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md). |
| XML-индексы | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Поддерживаются](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о службе базы данных SQL Azure см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Сведения о поддержке функций Transact-SQL в базе данных SQL Azure см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).

