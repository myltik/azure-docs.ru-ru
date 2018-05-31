---
title: Сравнение функций Базы данных SQL Azure | Документация Майкрософт
description: В данной статье сравниваются возможности Базы данных SQL Azure и управляемых экземпляров между собой и с SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: article
ms.date: 03/30/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 7e3b084f833b6d84e5c5102555eb586e306e9de8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895577"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Сравнение функций Базы данных SQL Azure и SQL Server 

В Базе данных SQL Azure и SQL Server используется общая база кода. Компоненты SQL Server, поддерживаемые в Базе данных SQL Azure, зависят от типа создаваемой базы данных SQL Azure. С помощью Базы данных SQL Azure можно создать базу данных в составе [управляемого экземпляра](sql-database-managed-instance.md) (в настоящее время эта функция находится на этапе общедоступной предварительной версии), а также можно создать отдельную базу данных либо базу данных в составе эластичного пула. 

Корпорация Майкрософт продолжает расширять возможности Базы данных SQL Azure. Посетите веб-страницу обновлений служб для Azure, чтобы получить последние обновления с помощью приведенных ниже фильтров.

* Фильтруйте обновления по [службе базы данных SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Фильтруйте обновления по [объявлениям об общедоступных версиях](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) функций базы данных SQL.

## <a name="sql-server-and-sql-database-feature-support"></a>Поддержка функций в SQL Server и базе данных SQL

В следующей таблице перечислены основные функции SQL Server, а также приведены сведения о поддержке каждой конкретной функции и ссылки на дополнительные сведения о ней. 

| **Функция SQL** | **Поддержка в базе данных SQL Azure** | **Управляемый экземпляр (предварительная версия)** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Да. Дополнительные сведения см. в статье [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище сертификатов Windows](sql-database-always-encrypted.md) и [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md). | Да. Дополнительные сведения см. в статье [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище сертификатов Windows](sql-database-always-encrypted.md) и [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md). |
| [Группы доступности AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Функция высокого уровня доступности](sql-database-high-availability.md) включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-business-continuity.md). | [Функция высокого уровня доступности](sql-database-high-availability.md) включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-business-continuity.md). |
| [Присоединение базы данных](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Нет  | Нет  |
| [Роли приложений](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Yes | Yes |
|[Аудит](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Да](sql-database-auditing.md)| [Да](sql-database-managed-instance-auditing.md) |
| [Автоматическое резервное копирование](sql-database-automated-backups.md) | Yes | Yes |
| [Автоматическая настройка (принудительное применение плана)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Да](sql-database-automatic-tuning.md)| [Да](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Автоматическая настройка (индексы)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Да](sql-database-automatic-tuning.md)| Нет  |
| [BACPAC-файл (экспорт)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Да. Дополнительные сведения см. в статье [Экспорт базы данных SQL Azure в BACPAC-файл](sql-database-export.md). | Yes |
| [BACPAC-файл (импорт)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Да. Дополнительные сведения см. в статье [Импорт BACPAC-файла в новую базу данных SQL Azure](sql-database-import.md). | Yes |
| [Команда BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Нет, только инициируемое системой автоматическое резервное копирование. См. раздел [Подробнее об автоматически создаваемых резервных копиях в Базе данных SQL](sql-database-automated-backups.md). | Инициируемое системой автоматическое резервное копирование и инициируемое пользователем резервное копирование (только копирование). См. раздел [Backup](sql-database-managed-instance-transact-sql-information.md#backup) (Резервное копирование). |
| [Встроенные функции](https://docs.microsoft.com/sql/t-sql/functions/functions) | Большинство. Дополнительные сведения см. в разделах по отдельным функциям. | Да. См. раздел [Stored procedures, functions, triggers](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) (Хранимые процедуры, функции и триггеры). |
| [Запись измененных данных](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Нет  | Yes |
| [отслеживание изменений;](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Yes |Yes |
| [Инструкции параметров сортировки](https://docs.microsoft.com/sql/t-sql/statements/collations) | Yes | Yes |
| [Индексы columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Да, [в категориях "Премиум" и "Стандартный" (S3 и выше), а также в категориях "Общего назначения" и "Критически важный для бизнеса"](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview). |Yes |
| [Среда CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Нет  | Да. См. раздел [CLR](sql-database-managed-instance-transact-sql-information.md#clr) (Среда CLR). |
| [автономные базы данных;](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Yes | Yes |
| [Автономные пользователи](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Yes | Yes |
| [Ключевые слова языка управления потоком](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Yes | Yes |
| [Запросы баз данных](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Нет. См. раздел [Обзор эластичных запросов к базе данных SQL Azure (предварительная версия)](sql-database-elastic-query-overview.md). | Да, включая [эластичные запросы](sql-database-elastic-query-overview.md). |
| [Межбазовые транзакции](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Нет  | Да — ознакомьтесь с [отличиями связанного сервера](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers). |
| [Курсоры](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Yes |Yes | 
| [Сжатие данных](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Yes |Yes |
| [Компонент Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Нет  | Yes |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Yes | Yes |
| [Зеркальное отображение базы данных](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Нет  | Нет  |
| [Параметры конфигурации базы данных](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Yes | Yes |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Нет  | Нет  |
| [Моментальные снимки базы данных](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Нет  | Нет  |
| [Типы данных](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Yes |Yes |
| [Инструкции DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. | Да. См. раздел [DBCC](sql-database-managed-instance-transact-sql-information.md#dbcc). |
| [Инструкции языка DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. | Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
| [Триггеры DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Только база данных |  Yes |
| [Распределенные представления секций](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Нет  | Yes |
| [Распределенные транзакции — MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Нет. Дополнительные сведения см. статье [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md). |  Нет. Дополнительные сведения см. статье [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md). |
| [Инструкции языка DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Yes | Yes |
| [Триггеры DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. |  Yes |
| [Динамические административные представления](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Большинство. Ознакомьтесь с разделами об отдельных динамических административных представлениях. |  Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
|[Динамическое маскирование данных](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Да](sql-database-dynamic-data-masking-get-started.md)| [Да](sql-database-dynamic-data-masking-get-started.md) |
| [Эластичные пулы](sql-database-elastic-pool.md) | Yes | Встроенная функция. У отдельного управляемого экземпляра может быть несколько баз данных, которые совместно используют один пул ресурсов. |
| [Уведомления о событиях](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Нет. Дополнительные сведения см. в статье [Создание оповещений для базы данных SQL Azure и хранилища данных с помощью портала Azure](sql-database-insights-alerts-portal.md). | Yes |
| [Выражения](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Yes | Yes |
| [Расширенные события](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Некоторые дополнительные сведения см. в статье [Расширенные события в Базе данных SQL](sql-database-xevent-db-diff-from-svr.md) | Да. См. раздел [Extended events](sql-database-managed-instance-transact-sql-information.md#extended-events) (Расширенные события). |
| [Расширенные хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Нет  | Нет  |
[Файлы и группы файлов](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Только первичная группа файлов | Yes |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Нет  | Нет  |
| [полнотекстовый поиск.](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Не поддерживаются сторонние средства разбиения текста на слова. |Не поддерживаются сторонние средства разбиения текста на слова. |
| [Функции](https://docs.microsoft.com/sql/t-sql/functions/functions) | Большинство. Дополнительные сведения см. в разделах по отдельным функциям. | Да. См. раздел [Stored procedures, functions, triggers](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) (Хранимые процедуры, функции и триггеры). |
| [Геовосстановление](sql-database-recovery-using-backups.md#geo-restore) | Yes | Нет. Вы можете восстанавливать периодические полные резервные копии COPY_ONLY. См. разделы [Backup](sql-database-managed-instance-transact-sql-information.md#backup) (Резервное копирование) и [RESTORE statement](sql-database-managed-instance-transact-sql-information.md#restore-statement) (Инструкция RESTORE). |
| [Георепликация](sql-database-geo-replication-overview.md) | Yes | Нет  |
| [Обработка Graph](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Yes | Yes |
| [Оптимизация в памяти](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Да, [только в категориях "Премиум" и "Критически важный для бизнеса"](sql-database-in-memory.md). | Нет  |
| [Поддержка данных JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Да](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Да](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Элементы языка](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным элементам. |  Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
| [Связанные серверы](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Нет. Дополнительные сведения см. в статье [Отчеты по масштабируемым облачным базам данных (предварительная версия)](sql-database-elastic-query-horizontal-partitioning.md). | Только в SQL Server и Базе данных SQL |
| [Доставка журналов](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Функция высокого уровня доступности](sql-database-high-availability.md) включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-business-continuity.md). |[Функция высокого уровня доступности](sql-database-high-availability.md) включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-business-continuity.md). |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Нет  | Нет  |
| [Минимальное ведение журнала при массовом импорте](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Нет  | Нет  |
| [Изменение системных данных](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Нет  | Yes |
| [Операции с индексом в сети](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Yes | Yes |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Нет |Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server).|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Yes|Yes|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Нет |Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server).|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Нет |Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server).|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Yes|Yes|
| [Операторы](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным операторам. |Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
| [Секционирование](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Yes | Yes |
| [Восстановление базы данных до точки во времени](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Да. Дополнительные сведения см. в разделе [Восстановление до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore). | Да. Дополнительные сведения см. в разделе [Восстановление до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore). |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Нет  | Нет  |
| [Управление на основе политик](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Нет  | Нет  |
| [Предикаты](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Yes | Yes |
| [Службы R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Предварительная версия. Ознакомьтесь с разделом [Новые возможности службы обучения машины в SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services).  | Нет  |
| [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Нет  | Yes |
| [Инструкции RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Нет  | Да. См. раздел [RESTORE statement](sql-database-managed-instance-transact-sql-information.md#restore-statement) (Инструкция RESTORE). |
| [Восстановление базы данных из резервной копии](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Только из создаваемых автоматически резервных копий. См. раздел [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md). | Из создаваемых автоматически резервных копий (см. раздел [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md)) и полных резервных копий (см. раздел [Backup](sql-database-managed-instance-transact-sql-information.md#backup) (Резервное копирование)). |
| [Безопасность на уровне строк](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Yes | Yes |
| [Семантический поиск](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Нет  | Нет  |
| [Порядковые номера](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Yes | Yes |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Нет  | Да. См. раздел [Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker). |
| [Параметры конфигурации сервера](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Нет  | Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
| [Инструкции SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным инструкциям. | Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server).|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Yes | Yes |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Yes | Yes |
| [Синхронизация данных SQL](sql-database-get-started-sql-data-sync.md) | Yes | Нет  |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Yes | Yes |
| [Агент SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Нет. Дополнительные сведения см. в статье [Начало работы с заданиями обработки эластичных баз данных](sql-database-elastic-jobs-getting-started.md) | Да. См. раздел [SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) (Агент SQL Server). |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Нет. Ознакомьтесь со страницей [служб Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). | Нет. Дополнительные сведения см. на странице [служб Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| [Аудит SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Нет. Дополнительные сведения см. в статье [Приступая к работе с аудитом базы данных SQL](sql-database-auditing.md). | Да. См. раздел [Auditing](sql-database-managed-instance-transact-sql-information.md#auditing) (Аудит). |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes | Yes |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Да, с помощью управляемых служб SSIS в среде фабрики данных Azure (ADF), где пакеты хранятся в SSISDB, размещенной в базе данных SQL Azure, и выполняются в [среде выполнения интеграции Azure SSIS](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md). | Да, с помощью управляемых служб SSIS в среде фабрики данных Azure (ADF), где пакеты хранятся в SSISDB, размещенной в управляемом экземпляре, и выполняются в [среде выполнения интеграции Azure SSIS](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Yes | Yes |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Yes | Yes |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Нет. Дополнительные сведения см. в статье о [расширенных событиях](sql-database-xevent-db-diff-from-svr.md). | Yes |
| [Репликация SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Только для подписчиков репликации транзакций и репликации моментального снимка](sql-database-cloud-migrate.md) | Нет  |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Нет. Ознакомьтесь с [Power BI](https://docs.microsoft.com/power-bi/). | Нет. Ознакомьтесь с [Power BI](https://docs.microsoft.com/power-bi/). |
| [Хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Yes | Yes |
| [Системные хранимые функции](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Большинство. Дополнительные сведения см. в разделах по отдельным функциям. | Да. См. раздел [Stored procedures, functions, triggers](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) (Хранимые процедуры, функции и триггеры). |
| [Системные хранимые процедуры](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным хранимым процедурам. | Да. См. раздел [Stored procedures, functions, triggers](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) (Хранимые процедуры, функции и триггеры). |
| [Системные таблицы](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным таблицам. | Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
| [Представления системных каталогов](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Некоторые. Дополнительные сведения см. в разделах по отдельным представлениям. | Да. См. раздел [T-SQL differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Отличия T-SQL от SQL Server). |
| [Временные таблицы](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Глобальные временные таблицы, хранимые локально, или для конкретных баз данных | Глобальные временные таблицы, хранимые локально, или для конкретных экземпляров |
| [Временные таблицы](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Да](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Да](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Обнаружение угроз|  [Да](sql-database-threat-detection.md)|[Да](sql-database-managed-instance-threat-detection.md)|
| [Флаги трассировки](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Нет  | Нет  |
| [Переменные](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Yes | Yes |
| [Прозрачное шифрование данных (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Yes | Нет, не на этапе общедоступной предварительной версии. |
[Виртуальная сеть](../virtual-network/virtual-networks-overview.md) | Частично. См. раздел [Использование конечных точек службы и правил виртуальной сети для базы данных SQL Azure](sql-database-vnet-service-endpoint-rule-overview.md). | Да, только для модели Azure Resource Manager. |
| [Отказоустойчивая кластеризация Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Функция высокого уровня доступности](sql-database-high-availability.md) включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-business-continuity.md). | [Функция высокого уровня доступности](sql-database-high-availability.md) включается в каждой базе данных. Сведения об аварийном восстановлении см. в статье [Обзор. Группы отработки отказа и активная георепликация](sql-database-business-continuity.md). |
| [XML-индексы](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Yes | Yes |

## <a name="next-steps"></a>Дополнительная информация

- Сведения о службе базы данных SQL Azure см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Сведения об управляемом экземпляре см. в статье [Общие сведения об Управляемом экземпляре Базы данных SQL Azure (предварительная версия)](sql-database-managed-instance.md).
