# Обзор
## [Что такое база данных SQL?](sql-database-technical-overview.md)
### [Уровни службы](sql-database-service-tiers.md)
### [Единицы транзакций базы данных](sql-database-what-is-a-dtu.md)
### [Обзор измерения производительности в DTU](sql-database-benchmark-overview.md)
### [Ограничения ресурсов](sql-database-resource-limits.md)
### [Функции](sql-database-features.md)
### [Часто задаваемые вопросы о базе данных SQL](sql-database-faq.md)
## Преимущества
### [Обучение и адаптация](sql-database-learn-and-adapt.md)
### [Масштабирование "на лету"](sql-database-scale-on-the-fly.md)
### [Создание мультитенантных приложений](sql-database-build-multi-tenant-apps.md)
### [Защита и безопасность](sql-database-helps-secures-and-protects.md)
### [Работа в вашей среде](sql-database-works-in-your-environment.md)
## Сравнения
### [Сравнение Базы данных SQL и SQL на виртуальной машине](sql-database-paas-vs-sql-server-iaas.md)
### [Отличия T-SQL](sql-database-transact-sql-information.md)
### [SQL и NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Инструменты Базы данных SQL](sql-database-manage-overview.md)
## [Учебники по Базе данных SQL](sql-database-explore-tutorials.md)
## [Решения для быстрого начала работы](sql-database-solution-quick-starts.md)
## Безопасность
### [Центр безопасности Azure и База данных SQL Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [Центр безопасности SQL](https://msdn.microsoft.com/library/azure/bb510589)
# Начало работы
## Базы данных и серверы
### Отдельные базы данных
#### Изучение
##### [Учебник по порталу Azure. Создание базы данных SQL Azure с помощью портала Azure](sql-database-get-started.md)
#### Рекомендации
##### [Учебник по порталу Azure. Создание базы данных SQL Azure с помощью портала Azure](sql-database-get-started.md)
##### [Учебник по порталу Azure. Создание базы данных SQL Azure с помощью PowerShell](sql-database-get-started-powershell.md)
##### [Учебник по порталу Azure. Создание базы данных SQL Azure с помощью C#](sql-database-get-started-csharp.md)
### Пулы эластичных баз данных
#### Изучение
##### [Пулы эластичных баз данных](sql-database-elastic-pool.md)
##### [Области применения пула эластичных баз данных](sql-database-elastic-pool-guidance.md)
##### [Цены на пул эластичных баз данных](sql-database-elastic-pool-price.md)
#### Рекомендации
##### [Создание с помощью портала Azure](sql-database-elastic-pool-create-portal.md)
##### [Создание с помощью PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Создание с помощью C#](sql-database-elastic-pool-create-csharp.md)
### Развертывание
#### Изучение
##### [Развертывание](sql-database-elastic-scale-introduction.md)
##### [Эластичное масштабирование](sql-database-elastic-scale-get-started.md)
##### [Создание масштабируемых облачных баз данных](sql-database-elastic-database-client-library.md)
##### [Перекрестные задания баз данных](sql-database-elastic-jobs-overview.md)
##### [Часто задаваемые вопросы по инструментам эластичных баз данных](sql-database-elastic-scale-faq.md)
##### [Глоссарий по средствам работы с эластичными базами данных](sql-database-elastic-scale-glossary.md)
##### [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md)
#### Рекомендации
##### [Задания обработки эластичных БД](sql-database-elastic-jobs-getting-started.md)
##### [Перекрестные отчеты по базам данных](sql-database-elastic-query-getting-started.md)
##### [Перекрестные запросы к базам данных](sql-database-elastic-query-getting-started-vertical.md)
##### [Удаление заданий обработки эластичных баз данных](sql-database-elastic-jobs-uninstall.md)
## Миграция и перемещение данных
### Изучение
#### [Перенос базы данных](sql-database-cloud-migrate.md)
#### [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Синхронизация данных](sql-database-get-started-sql-data-sync.md)
#### [Копирование базы данных SQL](sql-database-copy.md)
### Рекомендации
#### Определение совместимости базы данных
##### [Служебная программа пакета SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Устранение проблем совместимости базы данных
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [Мастер миграции SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [Мастер миграции SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Копирование базы данных
##### [Копирование с помощью портала Azure](sql-database-copy-portal.md)
##### [Копирование с помощью PowerShell](sql-database-copy-powershell.md)
##### [Копирование с помощью T-SQL](sql-database-copy-transact-sql.md)
#### Экспорт базы данных в BACPAC-файл
##### [Портал Azure](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [Служебная программа пакета SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Импорт базы данных из BACPAC-файла
##### [Портал Azure](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [Служебная программа пакета SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Загрузка из CSV-файла с помощью BCP](sql-database-load-from-csv-with-bcp.md)
## Аутентификация и предоставление доступа
### Изучение
#### [Обзор](sql-database-security.md)
#### [Рекомендации по безопасности](sql-database-security-guidelines.md)
#### [Управление именами для входа](sql-database-manage-logins.md)
### Рекомендации
### [Учебник. Аутентификация и управление доступом в среде SQL](sql-database-get-started-security.md)
## Защита и обеспечение безопасности данных
### Изучение
#### Аудит
##### [Аудит](sql-database-auditing-get-started.md)
##### [Поддержка клиентов прежних версий и изменения конечных точек с IP-адресами для аудита](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Обнаружение угроз](sql-database-threat-detection-get-started.md)
#### Шифрование данных
##### [Хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Хранилище сертификатов Windows](sql-database-always-encrypted.md)
##### [Прозрачное шифрование данных](https://msdn.microsoft.com/library/azure/dn948096)
##### [Шифрование столбцов](https://msdn.microsoft.com/library/azure/ms179331)
#### Маскирование данных
##### Динамическое маскирование данных
###### [Портал Azure](sql-database-dynamic-data-masking-get-started.md)
### Рекомендации
#### [Динамическое маскирование данных с помощью портала Azure](sql-database-dynamic-data-masking-get-started.md)
## Непрерывность бизнес-процессов
### Изучение
#### [Обзор](sql-database-business-continuity.md)
#### [Резервные копии базы данных](sql-database-automated-backups.md)
#### [Долгосрочное хранение](sql-database-long-term-retention.md) 
#### [Восстановление базы данных с использованием резервных копий](sql-database-recovery-using-backups.md)
#### [Требования к аутентификации для аварийного восстановления](sql-database-geo-replication-security-config.md)
#### [Сценарии проектирования непрерывности бизнес-процессов](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Стратегии аварийного восстановления с помощью пулов эластичных БД](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Последовательные обновления](sql-database-manage-application-rolling-upgrade.md)
### Рекомендации
#### Восстановление удаленной базы данных
##### [Портал Azure](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Восстановление до точки во времени
##### [Портал Azure](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Геовосстановление баз данных
##### [Портал Azure](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Отдельная таблица](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Восстановление после сбоя центра обработки данных](sql-database-disaster-recovery.md)
#### [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
### Репликация баз данных
#### [Обзор активной георепликации](sql-database-geo-replication-overview.md)
#### Настройка активной георепликации
##### [Портал Azure](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Отработка отказа с помощью активной георепликации
##### [Портал Azure](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Разработка приложений
### Изучение
#### [Обзор разработки приложения базы данных](sql-database-develop-overview.md)
#### [Библиотеки подключений](sql-database-libraries.md)
#### [Мультитенантные приложения SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Использование для ADO.NET 4.5 портов, отличных от порта 1433](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Получение необходимых значений для аутентификации приложения](sql-database-client-id-keys.md)
### Рекомендации
#### Подключение к приложению
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C и C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Подключение к Visual Studio](sql-database-connect-query.md)
#### [Создание клиентского приложения](https://www.microsoft.com/sql-server/developer-get-started)
#### [Работа с сообщениями об ошибках](sql-database-develop-error-messages.md)
### Пользовательские реализации программного обеспечения как услуги
#### [Программное обеспечение Daxko/CSI](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Разработка базы данных
### Изучение
#### Временные таблицы
##### [Временные таблицы](sql-database-temporal-tables.md)
##### [Политики хранения](sql-database-temporal-tables-retention-policy.md)
#### [Данные JSON](sql-database-json-features.md)
#### [В памяти](sql-database-in-memory.md)
### Рекомендации
#### [Разработка для SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
## Мониторинг и настройка
### Изучение
#### [Отдельные базы данных](sql-database-single-database-monitor.md)
#### [Обзор помощника по базам данных SQL](sql-database-advisor.md)
#### [Руководство по отдельным базам данных](sql-database-performance-guidance.md)
#### [Аналитика рабочих нагрузок на портале Azure](sql-database-performance.md)
#### [Использование пакетной обработки](sql-database-use-batching-to-improve-performance.md)
## База данных SQL версии 11
### [Прекращение использования Web и Business Edition](sql-database-web-business-sunset-faq.md)
### [Консультант по уровням служб](sql-database-service-tier-advisor.md)
### [Инструмент оценки пула эластичных баз данных](sql-database-elastic-pool-database-assessment-powershell.md)
### [Обновление до версии 12](sql-database-v12-plan-prepare-upgrade.md)
#### [Обновление с помощью портала Azure](sql-database-upgrade-server-portal.md)
#### [Обновление с помощью PowerShell](sql-database-upgrade-server-powershell.md)
#### [Рекомендации по выбору ценовой категории](sql-database-service-tier-advisor.md)
# Практическое руководство:
## Создание и управление
### Серверы и базы данных
#### [Отдельные базы данных](sql-database-manage-portal.md)
#### [Портал Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Пулы эластичных баз данных
#### [Создание с помощью портала Azure](sql-database-elastic-pool-create-portal.md)
#### [Создание с помощью PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [Создание с помощью C#](sql-database-elastic-pool-create-csharp.md)
#### [Управление с помощью портала Azure](sql-database-elastic-pool-manage-portal.md)
#### [Управление с помощью PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [Управление с помощью C#](sql-database-elastic-pool-manage-csharp.md)
#### [Управление с помощью T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Сегментированные базы данных
#### [Использование диспетчера карты сегментов](sql-database-elastic-scale-shard-map-management.md)
#### [Конфигурация безопасности при разбиении и слиянии](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Работа с Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Использование Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Маршрутизация, зависящая от данных](sql-database-elastic-scale-data-dependent-routing.md)
#### [Безопасность уровня строк в мультитенантном режиме](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Управление учетными данными](sql-database-elastic-scale-manage-credentials.md)
#### [Развертывание службы разбиения и объединения](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Добавление сегмента](sql-database-elastic-scale-add-a-shard.md)
#### [Устранение проблем сопоставления сегментов с помощью класса RecoveryManager](sql-database-elastic-database-recovery-manager.md)
###  Аутентификация
#### [Проверка подлинности Azure AD](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Правила файрволла
#### [портал Azure](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Задания и служба автоматизации
#### [Установка службы](sql-database-elastic-jobs-service-installation.md)
#### [Создание заданий обработки эластичных баз данных и управление ими на портале Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [Создание заданий обработки эластичных баз данных и управление ими с помощью PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Обновление клиентской библиотеки](sql-database-elastic-scale-upgrade-client-library.md)
#### [Управление базами данных SQL с помощью службы автоматизации Azure](sql-database-manage-automation.md)
### [Брандмауэр](sql-database-firewall-configure.md)
## Шифрование данных
### [Always Encrypted: обзор](sql-database-always-encrypted.md)
### [Прозрачное шифрование данных](https://msdn.microsoft.com/library/azure/dn948096)
### [Шифрование столбцов](https://msdn.microsoft.com/library/azure/ms179331)
## Миграция
### Определение совместимости
#### [Служебная программа пакета SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Устранение проблем совместимости
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Мастер миграции SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Использование мастера миграции SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Использование репликации транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Перенос существующих развернутых баз данных для дальнейшего развертывания](sql-database-elastic-convert-to-use-elastic-tools.md)
## Мониторинг и настройка
### [Анализ производительности запросов](sql-database-query-performance.md)
### [Помощник по работе с базами данных SQL](sql-database-advisor-portal.md)
### [Динамические административные представления](sql-database-monitoring-with-dmvs.md)
### [Уровни совместимости](sql-database-compatibility-level-query-performance-130.md)
### [Счетчики производительности для диспетчера карты сегментов](sql-database-elastic-database-perf-counters.md)
### [Советы по настройке производительности](sql-database-troubleshoot-performance.md)
### Изменение уровней служб и уровней производительности
#### [Использование портала Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [Создание оповещений](sql-database-insights-alerts-portal.md)
### In-Memory OLTP;
#### [Внедрение выполняющейся в памяти OLTP](sql-database-in-memory-oltp-migration.md)
#### [Мониторинг хранилища OLTP в памяти](sql-database-in-memory-oltp-monitoring.md)
### Хранилище запросов
#### [Мониторинг производительности с использованием хранилища запросов](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Сценарии использования хранилища запросов](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Работа с хранилищем запросов](sql-database-operate-query-store.md)
### Расширенные события
#### [Расширенные события](sql-database-xevent-db-diff-from-svr.md)
#### [Целевой код файла событий](sql-database-xevent-code-event-file.md)
#### [Целевой код кольцевого буфера](sql-database-xevent-code-ring-buffer.md)
## Перенос данных
### [Копирование базы данных SQL](sql-database-copy.md)
#### [Портал Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Экспорт базы данных в BACPAC-файл
#### [Портал Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Служебная программа пакета SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Импорт базы данных из BACPAC-файла
#### [Портал Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Служебная программа пакета SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Загрузка из CSV-файла с помощью BCP](sql-database-load-from-csv-with-bcp.md)
### [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md)
## Запрос
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Запросы к нескольким сегментам](sql-database-elastic-scale-multishard-querying.md)
### Запросы баз данных
#### [Обзор](sql-database-elastic-query-overview.md)
#### [Перекрестные запросы к базам данных с разными схемами](sql-database-elastic-query-vertical-partitioning.md)
#### [Перекрестные отчеты по базам данных](sql-database-elastic-query-horizontal-partitioning.md)
#### [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md)
## Восстановление
### Восстановление удаленной базы данных
#### [Портал Azure](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Восстановление до точки во времени
#### [Портал Azure](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Геовосстановление
#### [Портал Azure](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Отдельная таблица](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Восстановление после сбоя центра обработки данных](sql-database-disaster-recovery.md)
### [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
## Репликация
### [Обзор активной георепликации](sql-database-geo-replication-overview.md)
### Настройка
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Отработка отказа
#### [Портал Azure](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Устранение неполадок
### [Проблемы с подключением](sql-database-troubleshoot-common-connection-issues.md)
### [Временная ошибка подключения](sql-database-troubleshoot-connection.md)
### [Диагностика и предотвращение](sql-database-connectivity-issues.md)
### [Разрешения](sql-database-troubleshoot-permissions.md)
### [Перемещение баз данных](sql-database-troubleshoot-moving-data.md)


# Справочные материалы
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [PowerShell — классическая модель](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Командлеты базы данных SQL Azure](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
## [Командлеты для SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## Библиотека управления базами данных SQL
### [Справочник по библиотеке управления базами данных SQL](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Получение пакета библиотеки управления базами данных SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Драйверы SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Ресурсы
## [Цены](https://azure.microsoft.com/pricing/details/sql-database/)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Обновления службы](https://azure.microsoft.com/updates/?service=sql-database)
## [Инструменты SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [sqlcmd](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)




<!--HONumber=Nov16_HO4-->


