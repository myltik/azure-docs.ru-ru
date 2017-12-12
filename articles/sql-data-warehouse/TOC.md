# Обзор

## [Сведения о хранилище данных SQL Azure](sql-data-warehouse-overview-what-is.md)

# Быстрое начало работы

## [Создание подключения (портал)](create-data-warehouse-portal.md)

# Учебники
## [1. Загрузка данных из BLOB-объекта](load-data-from-azure-blob-storage-using-polybase.md)

# Основные понятия
## Функции службы
### [Архитектура MPP](massively-parallel-processing-mpp-architecture.md)
### [Уровни производительности](performance-tiers.md)
### [Единицы использования хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Резервные копии хранилища данных](sql-data-warehouse-backups.md)
### [Аудит](sql-data-warehouse-auditing-overview.md)
### [Ограничения емкости](sql-data-warehouse-service-capacity-limits.md)
### [Часто задаваемые вопросы](sql-data-warehouse-overview-faq.md)

## Безопасность
### [Обзор](sql-data-warehouse-overview-manage-security.md)
### [Аутентификация](sql-data-warehouse-authentication.md)


## Миграция в хранилище данных SQL.
### [Обзор](sql-data-warehouse-overview-migrate.md)
### [Служебная программа переноса](sql-data-warehouse-migrate-migration-utility.md)
### [Перенос схемы](sql-data-warehouse-migrate-schema.md)
### [Перенос кода](sql-data-warehouse-migrate-code.md)
### [Перенос данных](sql-data-warehouse-migrate-data.md)

## Загрузка и перемещение данных
### [Обзор](sql-data-warehouse-overview-load.md)
### [PolyBase;](sql-data-warehouse-load-polybase-guide.md)


## Интеграция
### [Обзор](sql-data-warehouse-overview-integrate.md)
### [Эластичные запросы в базе данных SQL](how-to-use-elastic-query-with-sql-data-warehouse.md)


## Мониторинг и настройка
### [Рекомендации](resource-classes-for-workload-management.md)
### [Сжатие ColumnStore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Мониторинг](sql-data-warehouse-manage-monitor.md)
### [Устранение неполадок](sql-data-warehouse-troubleshoot.md)

## Разработка хранилищ данных
### [Обзор](sql-data-warehouse-overview-develop.md)
### [Компоненты хранилища данных](sql-data-warehouse-overview-workload.md)

### Таблицы
#### [Обзор](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Типы данных](sql-data-warehouse-tables-data-types.md)
#### [Распределенные таблицы](sql-data-warehouse-tables-distribute.md)
#### [Индексы](sql-data-warehouse-tables-index.md)
#### [Удостоверение](sql-data-warehouse-tables-identity.md)
#### [Секции](sql-data-warehouse-tables-partition.md)
#### [Реплицированные таблицы](design-guidance-for-replicated-tables.md)
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

# Практические руководства
## Функции службы
### [Восстановление хранилища данных (портал)](sql-data-warehouse-restore-database-portal.md)
### [Восстановление хранилища данных (PowerShell)](sql-data-warehouse-restore-database-powershell.md)
### [Восстановление хранилища данных (REST API)](sql-data-warehouse-restore-database-rest-api.md)

## Безопасность
### [Включение шифрования (портал)](sql-data-warehouse-encryption-tde.md)
### [Включение шифрования (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
### [Обнаружение угроз](sql-data-warehouse-security-threat-detection.md)


## Загрузка и перемещение данных
### [AdventureWorks](sql-data-warehouse-load-sample-databases.md)
### [Хранилище озера данных Azure](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Фабрика данных](sql-data-warehouse-load-with-data-factory.md)
### [PolyBase из хранилища BLOB-объектов](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [PolyBase из SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [Redgate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Интеграция
### [Настройка эластичных запросов в базе данных SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Добавление задания Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Использование машинного обучения](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Визуализация с помощью Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Мониторинг и настройка
### [Анализ рабочей нагрузки](analyze-your-workload.md)

## Масштабирование
### [Управление вычислениями (портал)](sql-data-warehouse-manage-compute-portal.md)
### [Управление вычислениями (PowerShell)](sql-data-warehouse-manage-compute-powershell.md)
### [Управление вычислениями (REST API)](sql-data-warehouse-manage-compute-rest-api.md)
### [Автоматизация уровней вычислений](manage-compute-with-azure-functions.md)


# Справочные материалы


## T-SQL
### [Полный справочник](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Элементы языка для хранилища данных SQL](sql-data-warehouse-reference-tsql-language-elements.md)
### [Инструкции для хранилища данных SQL](sql-data-warehouse-reference-tsql-statements.md)
## [Системные представления](sql-data-warehouse-reference-tsql-system-views.md)
## [Командлеты PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=databases)
## [Форум](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Цены](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Запросы функций](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Поддержка](sql-data-warehouse-get-started-create-support-ticket.md)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Партнеры
### [Бизнес-аналитика](sql-data-warehouse-partner-business-intelligence.md)
### [Интеграция данных](sql-data-warehouse-partner-data-integration.md)
### [Управление данными](sql-data-warehouse-partner-data-management.md)
