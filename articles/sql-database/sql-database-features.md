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
ms.date: 10/23/2017
ms.author: carlrab
ms.openlocfilehash: c5853b7f4af02e096a0678de14abd5c0b3d7ab0c
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-sql-database-features"></a>Возможности базы данных SQL Azure

База данных SQL Azure и SQL Server используют общую базу кода и поддерживают преимущественно одинаковые функции на уровне базы данных. Основные отличия между функциями базы данных SQL Azure и SQL Server видны на уровне экземпляров. 

Мы продолжаем расширять возможности базы данных SQL Azure. Поэтому рекомендуем вам почаще посещать нашу веб-страницу с обновлениями служб Azure и при работе с ней пользоваться фильтрами.

* Фильтруйте обновления по [службе базы данных SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Фильтруйте обновления по [объявлениям об общедоступных версиях](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) функций базы данных SQL.

## <a name="sql-server-and-sql-database-feature-support"></a>Поддержка функций в SQL Server и базе данных SQL

В следующей таблице перечислены основные функции SQL Server, а также приведены сведения о поддержке каждой конкретной функции и ссылки на дополнительные сведения о функции. Сведения об отличиях Transact-SQL, которые следует учитывать при миграции имеющегося решения базы данных, см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).


| **Функция SQL Server** | **Поддержка в базе данных SQL Azure** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Да. Дополнительные сведения см. в статье [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище сертификатов Windows](sql-database-always-encrypted.md) и [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md).|
| [Группы доступности AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Функция высокого уровня доступности включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-geo-replication-overview.md). |
| [Присоединение базы данных](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Нет |
| [Роли приложений](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Да |
| [BACPAC-файл (экспорт)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Да. Дополнительные сведения см. в статье [Экспорт базы данных SQL Azure в BACPAC-файл](sql-database-export.md). |
| [BACPAC-файл (импорт)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Да. Дополнительные сведения см. в статье [Импорт BACPAC-файла в новую базу данных SQL Azure](sql-database-import.md). |
| [Команда BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Нет. Дополнительные сведения см. в статье [Подробнее об автоматически создаваемых резервных копиях в Базе данных SQL](sql-database-automated-backups.md). |
| [Встроенные функции](https://docs.microsoft.com/sql/t-sql/functions/functions) | Большинство. Дополнительные сведения см. в разделах по отдельным функциям. |
| [Запись измененных данных](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Нет |
| [отслеживание изменений;](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Да |
| [Инструкции параметров сортировки](https://docs.microsoft.com/sql/t-sql/statements/collations) | Да |
| [Индексы columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Да. [Только в Premium Edition.](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [Среда CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Нет |
| [автономные базы данных;](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Да |
| [Автономные пользователи](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Да |
| [Ключевые слова языка управления потоком](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Да |
| [Запросы баз данных](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Частично. Дополнительные сведения см. в статье [Обзор эластичных запросов к базе данных SQL Azure (предварительная версия)](sql-database-elastic-query-overview.md). |
| [Курсоры](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Да | 
| [Сжатие данных](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Да |
| [Компонент Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Нет |
| [Зеркальное отображение базы данных](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Нет |
| [Параметры конфигурации базы данных](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Да |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Нет |
| [Моментальные снимки базы данных](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Нет |
| [Типы данных](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Да |  
| [Инструкции DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. |
| [Инструкции языка DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Да |
| [Триггеры DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Только база данных |
| [Распределенные транзакции — MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Нет. Дополнительные сведения см. статье [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md). |
| [Инструкции языка DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Да |
| [Триггеры DML](https://docs.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. | 
| [Динамические административные представления](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Некоторые. Дополнительные сведения см. в разделах по отдельным динамическим административным представлениям. |
| [Уведомления о событиях](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Нет. Дополнительные сведения см. в статье [Создание оповещений для базы данных SQL Azure и хранилища данных с помощью портала Azure](sql-database-insights-alerts-portal.md). |
| [Выражения](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Да |
| [Расширенные события](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Некоторые дополнительные сведения см. в статье [Расширенные события в Базе данных SQL](sql-database-xevent-db-diff-from-svr.md) |
| [Расширенные хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Нет |
| [Файлы и группы файлов](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Только первичная группа файлов |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Нет |
| [полнотекстовый поиск.](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | Не поддерживаются сторонние средства разбиения текста на слова. |
| [Функции](https://docs.microsoft.com/sql/t-sql/functions/functions) | Большинство. Дополнительные сведения см. в разделах по отдельным функциям. |
| [Обработка Graph](/sql/relational-databases/graphs/sql-graph-overview) | Да |
| [Оптимизация в памяти](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Да. [Только в Premium Edition.](sql-database-in-memory.md) |
| [Поддержка данных JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Да |
| [Элементы языка](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным элементам. |  
| [Связанные серверы](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Нет. Дополнительные сведения см. в статье [Отчеты по масштабируемым облачным базам данных (предварительная версия)](sql-database-elastic-query-horizontal-partitioning.md). |
| [Доставка журналов](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Функция высокого уровня доступности включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-geo-replication-overview.md). |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Нет |
| [Минимальное ведение журнала при массовом импорте](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Нет |
| [Изменение системных данных](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Нет |
| [Операции с индексом в сети](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Да |
| [Операторы](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным операторам. |
| [Восстановление базы данных до точки во времени](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Да. Дополнительные сведения см. в разделе [Восстановление до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore). |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Нет |
| [Управление на основе политик](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Нет |
| [Предикаты](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Да |
| [Службы R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Предварительная версия. Ознакомьтесь с разделом [Новые возможности службы обучения машины в SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services).  |
| [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Нет |
| [Инструкции RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Нет | 
| [Восстановление базы данных из резервной копии](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Только из встроенных резервных копий. Дополнительные сведения см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md). |
| [Безопасность на уровне строк](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Да |
| [Семантический поиск](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Нет |
| [Порядковые номера](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Да |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Нет |
| [Параметры конфигурации сервера](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Нет |
| [Инструкции SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Да |
| [Агент SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Нет. Дополнительные сведения см. в статье [Начало работы с заданиями обработки эластичных баз данных](sql-database-elastic-jobs-getting-started.md) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Дополнительные сведения см. на странице [служб Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| [Аудит SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Нет. Дополнительные сведения см. в статье [Приступая к работе с аудитом базы данных SQL](sql-database-auditing.md). |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Дополнительные сведения см. на странице [о фабрике данных Azure](https://azure.microsoft.com/services/data-factory/). |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Да |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Нет. Дополнительные сведения см. в статье о [расширенных событиях](sql-database-xevent-db-diff-from-svr.md). |
| [Репликация SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Только для подписчиков репликации транзакций и репликации моментального снимка](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Нет |
| [Хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Да |
| [Системные хранимые функции](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным функциям. |
| [Системные хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным хранимым процедурам. |
| [Системные таблицы](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным таблицам. |
| [Представления системных каталогов](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным представлениям. |
| [Секционирование таблиц](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Да. Только первичная группа файлов. |
| [Временные таблицы](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | Только глобальные временные таблицы, хранимые локально, или для конкретных баз данных. |
| [Временные таблицы](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Да |
| [Переменные](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Да | 
| [Прозрачное шифрование данных (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Да |
| [Отказоустойчивая кластеризация Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Функция высокого уровня доступности включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-geo-replication-overview.md). |
| [XML-индексы](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Да |

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о службе базы данных SQL Azure см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Сведения о поддержке функций Transact-SQL в базе данных SQL Azure см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).
