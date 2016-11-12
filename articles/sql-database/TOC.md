# Обзор
## [Что такое база данных SQL?](sql-database-technical-overview.md)
## Функции
### [Уровни службы](sql-database-service-tiers.md)
### [Что такое DTU?](sql-database-what-is-a-dtu.md)
### [Обзор измерения производительности в DTU](sql-database-benchmark-overview.md)
### [Брандмауэр базы данных SQL и правила брандмауэра](sql-database-firewall-configure.md)
### [Средства управления](sql-database-manage-overview.md)
## Рекомендации и ограничения
### [Сравнение Базы данных SQL и SQL на виртуальной машине](sql-database-paas-vs-sql-server-iaas.md)
### [Отличия T-SQL](sql-database-transact-sql-information.md)
### [Ограничения ресурсов](sql-database-resource-limits.md)
### [Общие ограничения](sql-database-general-limitations.md)
## [Цены](https://azure.microsoft.com/pricing/details/sql-database/)
## [Новые возможности](https://azure.microsoft.com/updates/?service=sql-database)
## [Часто задаваемые вопросы о базе данных SQL](sql-database-faq.md)

## Преимущества
### [Обучение и адаптация](sql-database-learn-and-adapt.md)
### [Масштабирование "на лету"](sql-database-scale-on-the-fly.md)
### [Создание мультитенантных приложений](sql-database-build-multi-tenant-apps.md)
### [Защита и безопасность](sql-database-helps-secures-and-protects.md)
### [Работа в вашей среде](sql-database-works-in-your-environment.md)

## Сценарии
### Создание серверов, пулов, баз данных и брандмауэров, а также управление ими
#### Создание пулов эластичных баз данных и управление ими
#### [Области применения пула эластичных баз данных](sql-database-elastic-pool-guidance.md)
#### [Рекомендации по безопасности](sql-database-security-guidelines.md)
#### [Пулы эластичных баз данных](sql-database-elastic-pool.md)
#### [Автоматизация](sql-database-manage-automation.md)
#### Изменение уровней служб и уровней производительности
##### [Портал[Azure](sql-database-scale-up.md)
##### [PowerShell](sql-database-scale-up-powershell.md)
### Создание развернутых баз данных и управление ими
#### [Обзор](sql-database-elastic-scale-introduction.md)
#### [Создание масштабируемых облачных баз данных](sql-database-elastic-database-client-library.md)
#### [Развертывание с использованием диспетчера карты сегментов](sql-database-elastic-scale-shard-map-management.md)
#### [Перенос существующих баз данных для масштабирования](sql-database-elastic-convert-to-use-elastic-tools.md)
#### [Перемещение данных между развернутыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md)
#### [Использование Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Работа с Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Счетчики производительности для диспетчера карты сегментов](sql-database-elastic-database-perf-counters.md)
#### Задания и запросы, выполняемые между базами данных
##### [Перекрестные запросы к базам данных](sql-database-elastic-query-overview.md)
##### [Перекрестные запросы к базам данных с разными схемами](sql-database-elastic-query-vertical-partitioning.md)
##### [Перекрестные отчеты по базам данных](sql-database-elastic-query-horizontal-partitioning.md)
##### [Перекрестные задания баз данных](sql-database-elastic-jobs-overview.md)
##### [Маршрутизация, зависящая от данных](sql-database-elastic-scale-data-dependent-routing.md)
##### [Запросы к нескольким сегментам](sql-database-elastic-scale-multishard-querying.md)
##### [Безопасность уровня строк в мультитенантном режиме](sql-database-elastic-tools-multi-tenant-row-level-security.md)
##### [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md)
####[Глоссарий по средствам работы с эластичными базами данных](sql-database-elastic-scale-glossary.md)
#### [Часто задаваемые вопросы](sql-database-elastic-scale-faq.md)
### Создание и управление доступом и разрешениями
#### [Обзор](sql-database-security.md)
#### [Рекомендации по безопасности](sql-database-security-guidelines.md)
#### [Управление именами для входа](sql-database-manage-logins.md)
#### [Центр безопасности Azure и База данных SQL Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
#### [Центр безопасности SQL](https://msdn.microsoft.com/library/azure/bb510589)
### [Разработка баз данных и приложений](sql-database-develop-overview.md)
### Перенос баз данных
#### [Перенос базы данных SQL Server](sql-database-cloud-migrate.md)
### Обеспечение непрерывности бизнес-процессов
#### [Обзор](sql-database-business-continuity.md)
#### [Резервные копии базы данных](sql-database-automated-backups.md) 
#### [Восстановление базы данных с использованием резервных копий](sql-database-recovery-using-backups.md)
#### [Восстановление после сбоя центра обработки данных](sql-database-disaster-recovery.md)
#### [Требования к аутентификации для аварийного восстановления](sql-database-geo-replication-security-config.md)
#### [Сценарии проектирования непрерывности бизнес-процессов](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Стратегии аварийного восстановления с помощью пулов эластичных БД](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Последовательные обновления](sql-database-manage-application-rolling-upgrade.md)
### Мониторинг и настройка баз данных
#### [Отдельные базы данных](sql-database-single-database-monitor.md)
#### [Руководство по отдельным базам данных](sql-database-performance-guidance.md)
#### [Аналитика рабочих нагрузок на портале Azure](sql-database-performance.md)

## Реализация у клиентов
### [Программное обеспечение Daxko/CSI](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)

# Начало работы
## Создание серверов, пулов, баз данных и брандмауэров
### [Портал Azure](sql-database-get-started.md)
### [PowerShell](sql-database-get-started-powershell.md)
### [C#](sql-database-get-started-csharp.md)
## Запрос данных
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
## Управление серверами, пулами, базами данных и брандмауэрами
### [Портал Azure](sql-database-manage-portal.md)
### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### [PowerShell](sql-database-manage-powershell.md)
## [Управление доступом и разрешениями](sql-database-get-started-security.md)
## Защита и обеспечение безопасности данных
### [Аудит](sql-database-auditing-get-started.md)
### [Обнаружение угроз](sql-database-threat-detection-get-started.md)
### Динамическое маскирование данных
#### [Портал Azure](sql-database-dynamic-data-masking-get-started-portal.md)
## Создание развернутых баз данных и управление ими
### [Эластичное масштабирование](sql-database-elastic-scale-get-started.md)
### [Задания обработки эластичных БД](sql-database-elastic-jobs-getting-started.md)
### Эластичные запросы
### [Перекрестные отчеты по базам данных](sql-database-elastic-query-getting-started.md)
### [Перекрестные запросы к базам данных](sql-database-elastic-query-getting-started-vertical.md)
## [Оптимизация в памяти](sql-database-in-memory.md)
## [Перемещение баз данных](sql-database-troubleshoot-moving-data.md)
## [Синхронизация данных](sql-database-get-started-sql-data-sync.md)
##Мониторинг и настройка баз данных
### [Обзор помощника по базам данных SQL](sql-database-advisor.md)
### [Аналитика рабочих нагрузок на портале Azure](sql-database-performance.md)
## [Решения для быстрого начала работы](sql-database-solution-quick-starts.md)
# Практическое руководство:
## [Аудит](sql-database-auditing-get-started.md)
## Создание
### Группа ресурсов
#### [PowerShell](sql-database-manage-powershell.md#create-a-resource-group)
### Серверы
#### [Портал Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-server)
### Пулы эластичных баз данных
#### [Портал Azure](sql-database-elastic-pool-create-portal.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
### Базы данных
#### Отдельные базы данных
##### [Портал Azure](sql-database-get-started.md)
##### [C#](sql-database-get-started-csharp.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-get-started-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
#### Сегментированные базы данных
##### [Использование диспетчера карты сегментов](sql-database-elastic-scale-shard-map-management.md)
##### [Конфигурация безопасности при разбиении и слиянии](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Работа с Dapper](sql-database-elastic-scale-working-with-dapper.md)
##### [Использование Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
##### [Маршрутизация, зависящая от данных](sql-database-elastic-scale-data-dependent-routing.md)
##### [Безопасность уровня строк в мультитенантном режиме](sql-database-elastic-tools-multi-tenant-row-level-security.md)
### Правила файрволла
#### сервер;
##### [портал Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### База данных
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Задания
#### [Установка службы](sql-database-elastic-jobs-service-installation.md)
#### [Портал Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
### Количество входов в систему
#### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-logins.md)
## Разработка
### [Обзор](https://msdn.microsoft.com/library/mt763826.aspx)
### Сценарии
#### [Мультитенантные приложения SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### Временные таблицы
##### [Временные таблицы](sql-database-temporal-tables.md)
##### [Политики хранения](sql-database-temporal-tables-retention-policy.md)
#### [Данные JSON](sql-database-json-features.md)
#### [В памяти](sql-database-in-memory.md)
###Приступая к работе
#### [Библиотеки подключений](sql-database-libraries.md)
#### Подключение к приложению
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Подключение к Visual Studio](sql-database-connect-query.md)
### Практическое руководство
#### Создание серверов
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Создание пулов эластичных БД
###### [PowerShell](sql-database-elastic-pool-create-powershell.md)
###### [C#](sql-database-elastic-pool-create-csharp.md)
#### Создание баз данных
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Создание правил брандмауэра
###### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
###### [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
#### Управление серверами, пулами, базами данных и брандмауэрами
##### [PowerShell](sql-database-manage-powershell.md)
##### Управление пулами эластичных БД
###### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
###### [C#](sql-database-elastic-pool-manage-csharp.md)
##### [Изменение уровней служб и уровней производительности](sql-database-scale-up-powershell.md)
#### Перенос данных
##### [Экспорт базы данных в BACPAC-файл](sql-database-export-powershell.md)
##### [Импорт базы данных из BACPAC-файла](sql-database-import-powershell.md)
##### [Копирование базы данных в другое расположение Azure](sql-database-copy-powershell.md)
#### [Получение необходимых значений для аутентификации приложения](sql-database-client-id-keys.md)
#### [Задания обработки эластичных БД](sql-database-elastic-jobs-powershell.md)
#### Восстановление и база данных
##### Восстановление удаленной базы данных
###### [PowerShell](sql-database-restore-deleted-database-powershell.md)
##### Восстановление базы данных до точки во времени
###### [PowerShell](sql-database-point-in-time-restore-powershell.md)
##### Геовосстановление
###### [PowerShell](sql-database-geo-restore-powershell.md)
#### Репликация данных с помощью активной георепликации
##### Настройка
###### [PowerShell](sql-database-geo-replication-powershell.md)
##### Отработка отказа
###### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [Использование для ADO.NET 4.5 портов, отличных от порта 1433](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Работа с сообщениями об ошибках](sql-database-develop-error-messages.md)
#### [Использование пакетной обработки](sql-database-use-batching-to-improve-performance.md)
### Справочные материалы
#### [Transact-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
#### [Поставщик данных .NET Framework для SQL Server (понятия)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [Поставщик данных .NET Framework для SQL Server (справочник по API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### PowerShell для SQL
##### [Командлеты для Базы данных SQL (управление ресурсами)] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
##### [Командлеты для Базы данных SQL (управление службами)] (https://msdn.microsoft.com/library/azure/dn546723(v=azure.300\).aspx)
##### [Командлеты для SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
#### REST API базы данных SQL
##### [REST API (управление ресурсами)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST API (управление службами)](https://msdn.microsoft.com/library/azure/dn505719.aspx)
#### Библиотека управления базами данных SQL
##### [Справочник по библиотеке управления базами данных SQL](https://msdn.microsoft.com/library/azure/mt349017.aspx)
##### [Получение пакета библиотеки управления базами данных SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
#### Entity Framework
##### [Получение пакета Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Драйверы SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
##### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
##### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
##### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
##### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
##### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
##### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
##### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
#### [Пакет SDK для .NET (скачивание)](https://www.visualstudio.com/vs/azure-tools/)
#### [Пакет SDK для .NET (документация)](https://azure.microsoft.com/documentation/articles/dotnet-sdk/)
### Ресурсы
#### [Инструменты SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
#### [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
#### [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
#### [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
#### [sqlcmd](https://msdn.microsoft.com/library/ms162773.aspx)
#### [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## Удалить
### База данных
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database)
### сервер;
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database-server)
## Обнаружение угроз
### [Обнаружение угроз](sql-database-threat-detection-get-started.md)
### [Брандмауэр](sql-database-firewall-configure.md)
## Шифрование данных
### Постоянное шифрование
#### [Обзор постоянного шифрования](sql-database-always-encrypted.md)
#### [Хранилище ключей Azure для постоянного шифрования](sql-database-always-encrypted-azure-key-vault.md)
### [Прозрачное шифрование данных](https://msdn.microsoft.com/library/azure/dn948096)
### [Шифрование столбцов](https://msdn.microsoft.com/library/azure/ms179331)
## Управление
###  Аутентификация
#### Аутентификация SQL
#### [Проверка подлинности Azure Active Directory](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Серверы
### Пулы эластичных БД
#### [Портал Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### Базы данных
#### Отдельные базы данных
##### [Портал Azure](sql-database-manage-portal.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-blank)
#### Изменение уровней служб и уровней производительности
#### [Портал[Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-manage-powershell.md#change-the-performance-level-of-a-sql-database)
#### Сегментированные базы данных
##### [Перенос существующих развернутых баз данных для дальнейшего развертывания](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Управление учетными данными](sql-database-elastic-scale-manage-credentials.md)
##### [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md)
##### [Развертывание службы разбиения и объединения](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
##### [Добавление сегмента](sql-database-elastic-scale-add-a-shard.md)
##### [Устранение проблем сопоставления сегментов с помощью класса RecoveryManager](sql-database-elastic-database-recovery-manager.md)
### Правила файрволла
#### сервер;
##### [портал Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### База данных
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Задания
#### [Установка службы](sql-database-elastic-jobs-service-installation.md)
#### [Портал Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Обновление клиентской библиотеки](sql-database-elastic-scale-upgrade-client-library.md)
### Количество входов в систему
#### [Управление именами для входа](sql-database-manage-logins.md)
## Маскирование данных
### Динамическое маскирование данных
#### [Портал Azure](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Клиенты нижнего уровня](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
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
### Экспорт базы данных в BACPAC-файл
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Служебная программа пакета SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Импорт базы данных из BACPAC-файла
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Служебная программа пакета SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Портал Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)

## Мониторинг и настройка
### [Отдельные базы данных](sql-database-performance-guidance.md)
### Пулы эластичных БД
#### [Портал Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Анализ производительности запросов](sql-database-query-performance.md)
### помощник по базам данных SQL
#### [Портал Azure](sql-database-advisor-portal.md)
### Изменение уровней служб и уровней производительности
#### [Портал Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [Советы по настройке производительности](sql-database-troubleshoot-performance.md)
### In-Memory OLTP;
#### [Внедрение выполняющейся в памяти OLTP](sql-database-in-memory-oltp-migration.md)
#### [Мониторинг хранилища OLTP в памяти](sql-database-in-memory-oltp-monitoring.md)
### Хранилище запросов
#### [Мониторинг производительности с использованием хранилища запросов](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Сценарии использования хранилища запросов](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Работа с хранилищем запросов](sql-database-operate-query-store.md)
### [Уровни совместимости](sql-database-compatibility-level-query-performance-130.md)
### [Аудит событий](sql-database-auditing-get-started.md)
### [Счетчики производительности для диспетчера карты сегментов](sql-database-elastic-database-perf-counters.md)
### Расширенные события
#### [Расширенные события](sql-database-xevent-db-diff-from-svr.md)
#### [Целевой код файла событий](sql-database-xevent-code-event-file.md)
#### [Целевой код кольцевого буфера](sql-database-xevent-code-ring-buffer.md)
### DMV
#### [Динамические административные представления](sql-database-monitoring-with-dmvs.md)
#### [Динамические административные представления](sql-database-manage-azure-ssms#monitor-sql-database-using-dynamic-management-views


## Перенос данных
### Копирование базы данных SQL
#### [Обзор](sql-database-copy.md)
#### [Портал Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Экспорт базы данных в BACPAC-файл
#### [Портал Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [PowerShell](sql-database-export-powershell.md)
### Импорт базы данных из BACPAC-файла
#### [Портал Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
### [Синхронизация данных](sql-database-get-started-sql-data-sync.md)
### [Загрузка из CSV-файла с помощью BCP](sql-database-load-from-csv-with-bcp.md)

## Запрос
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Запросы к нескольким сегментам](sql-database-elastic-scale-multishard-querying.md)
### Перекрестные запросы к базам данных
#### [Перекрестные запросы к базам данных с разными схемами](sql-database-elastic-query-vertical-partitioning.md)
#### [Перекрестные отчеты по базам данных](sql-database-elastic-query-horizontal-partitioning.md)
#### [Распределенные транзакции по облачным базам данных](sql-database-elastic-transactions-overview.md)
#### [Обновление клиентской библиотеки](sql-database-elastic-scale-upgrade-client-library.md)
#### [Запросы к нескольким сегментам](sql-database-elastic-scale-multishard-querying.md)

## Восстановление
### Восстановление удаленной базы данных
### [Портал Azure](sql-database-restore-deleted-database-portal.md)
### [PowerShell](sql-database-restore-deleted-database-powershell.md)
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
### Соединение
#### [Проблемы с подключением](sql-database-troubleshoot-common-connection-issues.md)
#### [Временная ошибка подключения](sql-database-troubleshoot-connection.md)
#### [Диагностика и предотвращение](sql-database-connectivity-issues.md)
### [Разрешения](sql-database-troubleshoot-permissions.md)

# Справочные материалы
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## PowerShell для SQL
### [Командлеты для Базы данных SQL (управление ресурсами)] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
### [Командлеты для SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## REST API базы данных SQL
### [REST API (управление ресурсами)](https://msdn.microsoft.com/library/azure/mt420159)
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
## [Инструменты SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [sqlcmd](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)

<!--HONumber=Nov16_HO2-->


