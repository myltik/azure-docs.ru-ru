# Обзор

## [Что такое хранилище данных SQL?](sql-data-warehouse-overview-what-is.md)
## [Рабочая нагрузка хранилища данных](sql-data-warehouse-overview-workload.md)
## [Распределенные данные](sql-data-warehouse-distributed-data.md)

# Начало работы

## Создание хранилища данных SQL
### [Портал Azure](sql-data-warehouse-get-started-provision.md)
### [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)
## [Рекомендации по использованию хранилища данных SQL Azure](sql-data-warehouse-best-practices.md)
## [Управление](sql-data-warehouse-overview-manage.md)
## [Получение поддержки](sql-data-warehouse-get-started-create-support-ticket.md)

# Практическое руководство:

## Архивация и восстановление

### [Обзор службы архивации](sql-data-warehouse-backups.md)
### [Обзор восстановления](sql-data-warehouse-restore-database-overview.md)
#### [Портал Azure](sql-data-warehouse-restore-database-portal.md)
#### [PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Подключение

### [Обзор](sql-data-warehouse-connect-overview.md)
### [Строки подключения](sql-data-warehouse-connection-strings.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Установка Visual Studio](sql-data-warehouse-install-visual-studio.md)

## Разработка

### [Обзор](sql-data-warehouse-overview-develop.md)

### Таблицы

#### [Обзор](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Типы данных](sql-data-warehouse-tables-data-types.md)
#### [Распределенные таблицы](sql-data-warehouse-tables-distribute.md)
#### [Индексы](sql-data-warehouse-tables-index.md)
#### [Секции](sql-data-warehouse-tables-partition.md)
#### [Статистика](sql-data-warehouse-tables-statistics.md)
#### [Временные](sql-data-warehouse-tables-temporary.md)

### Запросы

#### [Динамический SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [Группировка по параметрам](sql-data-warehouse-develop-group-by-options.md)
#### [Метки](sql-data-warehouse-develop-label.md)

### Элементы языка T-SQL

#### [Циклы](sql-data-warehouse-develop-loops.md)
#### [Хранимые процедуры](sql-data-warehouse-develop-stored-procedures.md)
#### [Транзакции](sql-data-warehouse-develop-transactions.md)
#### [Рекомендации по транзакциям](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Пользовательские схемы](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Присвоение значения переменной](sql-data-warehouse-develop-variable-assignment.md)
#### [Представления](sql-data-warehouse-develop-views.md)

## Интеграция

### [Обзор](sql-data-warehouse-overview-integrate.md)
### [Фабрика данных](sql-data-warehouse-integrate-azure-data-factory.md)
### [Машинное обучение](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Учебник по машинному обучению](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Визуализация Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

## загрузить

### [Обзор](sql-data-warehouse-overview-load.md)
### [Пример данных](sql-data-warehouse-load-sample-databases.md)
### [AzCopy](sql-data-warehouse-load-from-sql-server-with-azcopy.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [BCP из SQL Server](sql-data-warehouse-load-from-sql-server-with-bcp.md)
### [Фабрика данных](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
### [Фабрика данных из хранилища BLOB-объектов](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
### [PolyBase;](sql-data-warehouse-get-started-load-with-polybase.md)
### [Руководство по PolyBase](sql-data-warehouse-load-polybase-guide.md)
### [PolyBase из хранилища BLOB-объектов](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [PolyBase из SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [Redgate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Миграция

### [Обзор](sql-data-warehouse-overview-migrate.md)
### [Служебная программа переноса](sql-data-warehouse-migrate-migration-utility.md)
### [Перенос схемы](sql-data-warehouse-migrate-schema.md)
### [Перенос кода](sql-data-warehouse-migrate-code.md)
### [Перенос данных](sql-data-warehouse-migrate-data.md)
### [Переход на использование хранилища класса Premium](sql-data-warehouse-migrate-to-premium-storage.md)

## Приостановка и масштабирование

### [Обзор](sql-data-warehouse-manage-compute-overview.md)
### [Портал Azure](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [ИНТЕРФЕЙС REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## Производительность

### [Обзор](sql-data-warehouse-overview-manage-user-queries.md)
### [Сжатие ColumnStore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Запросы](sql-data-warehouse-manage-monitor.md)
### [Рабочая нагрузка](sql-data-warehouse-develop-concurrency.md)
### [Устранение неполадок](sql-data-warehouse-troubleshoot.md)

## Безопасность

### [Обзор](sql-data-warehouse-overview-manage-security.md)
### [Аудит](sql-data-warehouse-auditing-overview.md)
### [Аудит клиентов нижнего уровня](sql-data-warehouse-auditing-downlevel-clients.md)
### [Аутентификация](sql-data-warehouse-authentication.md)
### [Шифрование](sql-data-warehouse-encryption-tde.md)
### [Шифрование с помощью T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Обнаружение угроз](sql-data-warehouse-security-threat-detection.md)

# Справочные материалы

## [Ограничения емкости](sql-data-warehouse-service-capacity-limits.md)
## [Элементы языка T-SQL](sql-data-warehouse-reference-tsql-language-elements.md)
## [Инструкции T-SQL](sql-data-warehouse-reference-tsql-statements.md)
## [Системные представления T-SQL](sql-data-warehouse-reference-tsql-system-views.md)
## [Командлеты PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Ресурсы
## [Цены](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Форум](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Партнеры
### [Бизнес-аналитика](sql-data-warehouse-partner-business-intelligence.md)
### [Интеграция данных](sql-data-warehouse-partner-data-integration.md)
### [Управление данными](sql-data-warehouse-partner-data-management.md)


<!--HONumber=Nov16_HO3-->


