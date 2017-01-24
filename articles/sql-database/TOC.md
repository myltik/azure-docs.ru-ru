# Обзор
## [Что такое база данных SQL?](sql-database-technical-overview.md)
### [Уровни службы](sql-database-service-tiers.md)
### [DTU и eDTU](sql-database-what-is-a-dtu.md)
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
## Сравнение
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
### Изучение
#### [Серверы](sql-database-server-overview.md)
#### [Отдельные базы данных](sql-database-overview.md)
#### [Несколько баз данных](sql-database-elastic-scale-introduction.md)
##### Сопоставление клиентов
###### [Эластичная клиентская библиотека](sql-database-elastic-database-client-library.md)
###### [Диспетчер карты сегментов](sql-database-elastic-scale-shard-map-management.md)
###### [Маршрутизация с зависимостью от данных](sql-database-elastic-scale-data-dependent-routing.md)
###### [Управление учетными данными](sql-database-elastic-scale-manage-credentials.md)
###### [Запросы к нескольким сегментам](sql-database-elastic-scale-multishard-querying.md)
##### Пулы эластичных БД (пулы ресурсов)
###### [Что такое пул эластичных БД?](sql-database-elastic-pool.md)
###### [Области применения пула эластичных БД](sql-database-elastic-pool-guidance.md)
###### [Цены на пул эластичных баз данных](sql-database-elastic-pool-price.md)
##### Сегментированные базы данных
###### [Глоссарий по средствам работы с эластичными базами данных](sql-database-elastic-scale-glossary.md)
###### [Перемещение данных между сегментами](sql-database-elastic-scale-overview-split-and-merge.md)
###### [Часто задаваемые вопросы по инструментам эластичных баз данных](sql-database-elastic-scale-faq.md)
##### Запрос к эластичной БД (запросы между базами данных)
###### [Что такое запрос к эластичной БД?](sql-database-elastic-query-overview.md)
##### Транзакции эластичных БД (распределенные транзакции)
###### [Транзакции между облачными базами данных](sql-database-elastic-transactions-overview.md)
##### Задания обработки эластичных БД (задания между базами данных)
###### [Что такое задание обработки эластичных БД?](sql-database-elastic-jobs-overview.md)
#### [Использование Azure RemoteApp для подключения к базе данных SQL](sql-database-ssms-remoteapp.md)
#### [Управление базами данных SQL с помощью службы автоматизации Azure](sql-database-manage-automation.md)
### Рекомендации
#### [Создание отдельной базы данных с помощью портала Azure](sql-database-get-started.md)
#### [Создание отдельной базы данных с помощью PowerShell](sql-database-get-started-powershell.md)
#### [Создание отдельной базы данных с помощью C#](sql-database-get-started-csharp.md)
#### [Создание сегментированного приложения](sql-database-elastic-scale-get-started.md)
#### [Перемещение данных между сегментами](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Начало работы с заданиями обработки эластичных БД](sql-database-elastic-jobs-getting-started.md)
#### [Начало работы с запросами к эластичным БД](sql-database-elastic-query-getting-started-vertical.md)
#### [Создание отчетов с помощью запросов к эластичным БД](sql-database-elastic-query-getting-started.md)
#### [Запросы к базам данных с использованием различных схем](sql-database-elastic-query-vertical-partitioning.md)
#### [Отчеты по масштабируемым базам данных](sql-database-elastic-query-horizontal-partitioning.md)
## Миграция и перемещение данных
### Изучение
#### [Перенос базы данных](sql-database-cloud-migrate.md)
#### [Репликация транзакций](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Синхронизация данных](sql-database-get-started-sql-data-sync.md)
#### [Копирование базы данных SQL](sql-database-copy.md)
## Правила брандмауэра, проверка подлинности и авторизация
### Изучение
#### [Обзор](sql-database-security.md)
#### [Рекомендации по безопасности](sql-database-security-guidelines.md)
#### [Брандмауэр](sql-database-firewall-configure.md)
#### [Управление именами для входа](sql-database-manage-logins.md)
### Рекомендации
#### [Проверка подлинности и авторизация в SQL](sql-database-get-started-security.md)
## Защита и обеспечение безопасности данных
### Изучение
#### Аудит
##### [Аудит](sql-database-auditing-get-started.md)
##### [Поддержка клиентов прежних версий и изменения конечных точек с IP-адресами для аудита](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Обнаружение угроз](sql-database-threat-detection-get-started.md)
#### Шифрование данных
##### [Хранилище ключей Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Прозрачное шифрование данных](https://msdn.microsoft.com/library/azure/dn948096)
##### [Шифрование столбцов](https://msdn.microsoft.com/library/azure/ms179331)
#### Маскирование данных
##### Динамическое маскирование данных
###### [Портал Azure](sql-database-dynamic-data-masking-get-started.md)
### Рекомендации
#### [Динамическое маскирование данных с помощью портала Azure](sql-database-dynamic-data-masking-get-started.md)
##### [Always Encrypted с использованием хранилища сертификатов Windows](sql-database-always-encrypted.md)
## Непрерывность бизнес-процессов
### Изучение
#### [Обзор](sql-database-business-continuity.md)
#### [Резервные копии базы данных](sql-database-automated-backups.md)
#### [Длительное хранение](sql-database-long-term-retention.md)
#### [Восстановление базы данных с использованием резервных копий](sql-database-recovery-using-backups.md)
#### [Восстановление отдельной таблицы](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Восстановление после сбоя центра обработки данных](sql-database-disaster-recovery.md)
#### [Требования к аутентификации для аварийного восстановления](sql-database-geo-replication-security-config.md)
#### [Сценарии проектирования непрерывности бизнес-процессов](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Стратегии аварийного восстановления с помощью пулов эластичных БД](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Последовательные обновления](sql-database-manage-application-rolling-upgrade.md)
#### [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
#### [Обзор активной георепликации](sql-database-geo-replication-overview.md)
### Рекомендации
#### [Начало работы с резервным копированием и восстановлением базы данных SQL](sql-database-get-started-backup-recovery.md)
## Разработка приложений
### Изучение
#### [Обзор разработки приложения базы данных](sql-database-develop-overview.md)
#### [Библиотеки подключений](sql-database-libraries.md)
#### [Мультитенантные приложения SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Масштабирование мультитенантных приложений SaaS с безопасностью на уровне строк](sql-database-elastic-tools-multi-tenant-row-level-security.md)
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
#### [Использование Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Использование клиентской библиотеки с Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Варианты пользовательской реализации
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
#### [Оптимизация в памяти](sql-database-in-memory.md)
### Рекомендации
#### [Разработка для SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Внедрение выполняющейся в памяти OLTP](sql-database-in-memory-oltp-migration.md)
## Мониторинг и настройка
### Изучение
#### [Отдельные базы данных](sql-database-single-database-monitor.md)
#### [Обзор помощника по базам данных SQL](sql-database-advisor.md)
#### [Руководство по отдельным базам данных](sql-database-performance-guidance.md)
#### [Аналитика рабочих нагрузок на портале Azure](sql-database-performance.md)
#### [Использование пакетной обработки](sql-database-use-batching-to-improve-performance.md)
#### [Расширенные события](sql-database-xevent-db-diff-from-svr.md)
## База данных SQL версии 11
### [Прекращение использования Web и Business Edition](sql-database-web-business-sunset-faq.md)
### [Консультант по уровням служб](sql-database-service-tier-advisor.md)
### [Инструмент оценки пула эластичных баз данных](sql-database-elastic-pool-database-assessment-powershell.md)
### [Обновление до версии 12](sql-database-v12-plan-prepare-upgrade.md)
#### [Обновление с помощью портала Azure](sql-database-upgrade-server-portal.md)
#### [Обновление с помощью PowerShell](sql-database-upgrade-server-powershell.md)
# Практическое руководство
## Создание и управление
### [Управление базой данных SQL с помощью портала Azure](sql-database-manage-portal.md)
### [Управление базой данных SQL с помощью PowerShell](sql-database-manage-powershell.md)
### [Управление базой данных SQL с помощью SSMS](sql-database-manage-azure-ssms.md)
### Серверы
#### [Создание серверов](sql-database-create-servers.md)
#### [Просмотр и обновление параметров сервера](sql-database-view-update-server-settings.md)
### Отдельные базы данных
#### [Создание отдельных баз данных](sql-database-create-databases.md)
#### [Просмотр и обновление параметров базы данных](sql-database-view-update-database-settings.md)
### Правила файрволла
#### [Создание правил брандмауэра с помощью портала Azure](sql-database-configure-firewall-settings.md)
#### [Создание правил брандмауэра с помощью PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Создание правил брандмауэра с помощью REST API](sql-database-configure-firewall-settings-rest.md)
#### [Создание правил брандмауэра с помощью T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Несколько баз данных
#### [Обновление клиентской библиотеки в клиентских приложениях](sql-database-elastic-scale-upgrade-client-library.md)
#### Сегментированные базы данных
##### [Конфигурация системы безопасности](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Добавление сегмента](sql-database-elastic-scale-add-a-shard.md)
##### [Устранение проблем с картой сегментов](sql-database-elastic-database-recovery-manager.md)
##### [Преобразование существующих масштабированных баз данных в сегментированные базы данных](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Создание счетчиков производительности для диспетчера карты сегментов](sql-database-elastic-database-perf-counters.md)
#### Задания обработки эластичных БД
##### [Как установить службу заданий обработки эластичных БД?](sql-database-elastic-jobs-service-installation.md)
##### [Создание заданий обработки эластичных баз данных и управление ими с помощью PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Создание заданий обработки эластичных БД и управление ими на портале Azure](sql-database-elastic-jobs-create-and-manage.md)
##### [Как удалить задания обработки эластичных БД?](sql-database-elastic-jobs-uninstall.md)
#### Пулы эластичных БД
##### [Создание с помощью портала Azure](sql-database-elastic-pool-create-portal.md)
##### [Создание с помощью PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Создание с помощью C#](sql-database-elastic-pool-create-csharp.md)
##### [Управление с помощью портала Azure](sql-database-elastic-pool-manage-portal.md)
##### [Управление с помощью PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Управление с помощью C#](sql-database-elastic-pool-manage-csharp.md)
##### [Управление с помощью T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Проверка подлинности и авторизация
### [Проверка подлинности Azure AD](sql-database-aad-authentication.md)
### [Многофакторная проверка подлинности](sql-database-ssms-mfa-authentication.md)
## Шифрование данных
### [Прозрачное шифрование данных](https://msdn.microsoft.com/library/azure/dn948096)
### [Шифрование столбцов](https://msdn.microsoft.com/library/azure/ms179331)
## Перенос баз данных
### Определение совместимости
#### [Определение совместимости с помощью служебной программы пакета SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Определение совместимости с помощью SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Устранение проблем совместимости
#### [Устранение проблем совместимости с помощью SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Устранение проблем совместимости с помощью SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Устранение проблем совместимости с помощью SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Перенос с помощью мастера миграции SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Мониторинг и настройка
### [Анализ производительности запросов](sql-database-query-performance.md)
### [Помощник по работе с базами данных SQL](sql-database-advisor-portal.md)
### [Динамические административные представления](sql-database-monitoring-with-dmvs.md)
### [Уровни совместимости](sql-database-compatibility-level-query-performance-130.md)
### [Советы по настройке производительности](sql-database-troubleshoot-performance.md)
### Изменение уровней служб и уровней производительности
#### [Изменение уровней служб с помощью портала Azure](sql-database-scale-up.md)
#### [Изменение уровней служб с помощью PowerShell](sql-database-scale-up-powershell.md)
### [Создание оповещений](sql-database-insights-alerts-portal.md)
#### [Мониторинг хранилища OLTP в памяти](sql-database-in-memory-oltp-monitoring.md)
### Хранилище запросов
#### [Мониторинг производительности с использованием хранилища запросов](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Сценарии использования хранилища запросов](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Работа с хранилищем запросов](sql-database-operate-query-store.md)
### Расширенные события
#### [Целевой код файла событий](sql-database-xevent-code-event-file.md)
#### [Целевой код кольцевого буфера](sql-database-xevent-code-ring-buffer.md)
## Перенос данных
### Копирование базы данных SQL
#### [Копирование с помощью портала Azure](sql-database-copy-portal.md)
#### [Копирование с помощью PowerShell](sql-database-copy-powershell.md)
#### [Копирование с помощью T-SQL](sql-database-copy-transact-sql.md)
### Экспорт базы данных в BACPAC-файл
#### [Экспорт с помощью портала Azure](sql-database-export.md)
#### [Экспорт с помощью SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Экспорт с помощью служебной программы пакета SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Экспорт с помощью PowerShell](sql-database-export-powershell.md)
### Импорт базы данных из BACPAC-файла
#### [Импорт с помощью портала Azure](sql-database-import.md)
#### [Импорт с помощью PowerShell](sql-database-import-powershell.md)
#### [Импорт с помощью SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Импорт с помощью служебной программы пакета SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Загрузка из CSV-файла с помощью BCP](sql-database-load-from-csv-with-bcp.md)
## Запрос
### [Создание запросов с помощью SSMS](sql-database-connect-query-ssms.md)
## Архивация и восстановление
### Долгосрочное хранение резервных копий
#### [Настройка долгосрочного хранения резервных копий](sql-database-configure-long-term-retention.md)
#### [Просмотр резервных копий в хранилище служб восстановления](sql-database-view-backups-in-vault.md)
#### [Восстановление после долгосрочного хранения резервных копий](sql-database-restore-from-long-term-retention.md)
### Восстановление удаленной базы данных
#### [Восстановление удаленной базы данных с помощью портала Azure](sql-database-restore-deleted-database-portal.md)
#### [Восстановление удаленной базы данных с помощью PowerShell](sql-database-restore-deleted-database-powershell.md)
### Восстановление до точки во времени
#### [Восстановление до точки во времени](sql-database-point-in-time-restore.md)
#### [Просмотр самой старой точки восстановления](sql-database-view-oldest-restore-point.md)
### Геовосстановление
#### [Геовосстановление с помощью портала Azure](sql-database-geo-restore-portal.md)
#### [Геовосстановление с помощью PowerShell](sql-database-geo-restore-powershell.md)
## Активная георепликация
### [Настройка с помощью портала Azure](sql-database-geo-replication-portal.md)
### [Настройка с помощью PowerShell](sql-database-geo-replication-powershell.md)
### [Настройка с помощью T-SQL](sql-database-geo-replication-transact-sql.md)
### [Отработка отказа с помощью портала Azure](sql-database-geo-replication-failover-portal.md)
### [Отработка отказа с помощью PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Отработка отказа с помощью T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Устранение неполадок
### [Проблемы с подключением](sql-database-troubleshoot-common-connection-issues.md)
### [Временная ошибка подключения](sql-database-troubleshoot-connection.md)
### [Диагностика и предотвращение](sql-database-connectivity-issues.md)
### [Разрешения](sql-database-troubleshoot-permissions.md)
### [Перемещение баз данных](sql-database-troubleshoot-moving-data.md)
# Справочные материалы
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (эластичная база данных)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Сопутствующие материалы
## Библиотека управления базами данных SQL
### [Получение пакета библиотеки управления базами данных SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Драйверы SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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


<!--HONumber=Jan17_HO3-->


