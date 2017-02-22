
# Обзор
## [Что такое DocumentDB?](documentdb-introduction.md)
## [Ключевые понятия](documentdb-resources.md)
## [Глобальное распределение](documentdb-distribute-data-globally.md)
## [Анализ совокупной стоимости владения базой данных NoSQL](https://aka.ms/documentdb-tco-paper)
## Сценарии
### [Распространенные варианты использования](documentdb-use-cases.md)
### [Использование социальных сетей с DocumentDB](documentdb-social-media-apps.md)

# Начало работы
## Создание первого приложения
### [Консольное приложение .NET](documentdb-get-started.md)
### [Консольное приложение .NET Core](documentdb-dotnetcore-get-started.md)
### [Консольное приложение Java](documentdb-java-get-started.md)
### [Консольное приложение Node.js](documentdb-nodejs-get-started.md)
### [Консольное приложение C++](documentdb-cpp-get-started.md)
## Создание веб-приложения
### [Веб-приложение .NET](documentdb-dotnet-application.md)
### [Веб-приложение Node.js](documentdb-nodejs-application.md)
### [Веб-приложение Java](documentdb-java-application.md)
### [Веб-приложения Python Flask](documentdb-python-application.md)
## [Разработка в локальной среде](documentdb-nosql-local-emulator.md)
### [Экспорт сертификатов в эмуляторе](documentdb-nosql-local-emulator-export-ssl-certificates.md)
## [Часто задаваемые вопросы](documentdb-faq.md)

# Практическое руководство

## План
### [Секционирование и масштабирование](documentdb-partition-data.md)
### [Целостность](documentdb-consistency-levels.md)
### [Базы данных NoSQL и SQL](documentdb-nosql-vs-sql.md)
### [Экономичные операции чтения и записи](documentdb-key-value-store-cost.md)

## Управление
### [Импорт данных](documentdb-import-data.md)
### [Моделирование данных](documentdb-modeling-data.md)
### [Использование геопространственных данных](documentdb-geospatial.md)
### [Разработка для нескольких регионов](documentdb-developing-with-multiple-regions.md)
### [Отработка отказа между регионами](documentdb-regional-failovers.md)
### [Автоматическое истечение срока хранения данных](documentdb-time-to-live.md)
### [Настройка индексов](documentdb-indexing-policies.md)
### [Безопасный доступ к данным](documentdb-secure-access-to-data.md)
### [Архивация и восстановление](documentdb-online-backup-and-restore.md)
### [Снятие с учета S1, S2, S3](documentdb-performance-levels.md)
### [Единиц запросов](documentdb-request-units.md)
### Инфраструктура CLI Azure
#### [Создание учетной записи](documentdb-automation-resource-manager-cli.md)
#### [Добавление и удаление регионов](documentdb-automation-region-management.md)
### [Azure PowerShell](documentdb-manage-account-with-powershell.md)
### [Поддерживаемые брандмауэры](documentdb-firewall-support.md)

## Разработка
### [SQL query](documentdb-sql-query.md)
### [Хранимые процедуры, триггеры и определяемые пользователем функции](documentdb-programming.md)
### [Отслеживание изменений с помощью веб-канала изменений](documentdb-change-feed.md)
### [Тестирование производительности](documentdb-performance-testing.md)
### [Советы по улучшению производительности](documentdb-performance-tips.md)
### Рекомендации
#### [Архитектура с несколькими источниками](documentdb-multi-region-writers.md)
#### [Дата и время](documentdb-working-with-dates.md)

## Поддержка DocumentDB для MongoDB
### [Протокол поддержки для MongoDB](documentdb-protocol-mongodb.md)
### [Создание учетной записи](documentdb-create-mongodb-account.md)
### [Подключение к учетной записи](documentdb-connect-mongodb-account.md)
### [Перенос данных](documentdb-mongodb-migrate.md)
### [Использование Robomongo](documentdb-mongodb-robomongo.md)
### [Использование MongoChef](documentdb-mongodb-mongochef.md)
### [Примеры поддержки протокола](documentdb-mongodb-samples.md)

## Использование портала
### [Создание учетной записи базы данных](documentdb-create-account.md)
### [Создание коллекции](documentdb-create-collection.md)
### [Настройка пропускной способности](documentdb-set-throughput.md)
### [Добавление глобальной репликации](documentdb-portal-global-replication.md)
### [Добавление и редактирование документов](documentdb-view-json-document-explorer.md)
### [Запрос документов](documentdb-query-collections-query-explorer.md)
### [Управление учетной записью](documentdb-manage-account.md)
### [Мониторинг учетной записи](documentdb-monitor-accounts.md)
### [Управление сценариями](documentdb-view-scripts.md)
### [Советы по устранению неполадок](documentdb-portal-troubleshooting.md)


## Интеграция
### [Развертывание веб-сайта в службе приложений Azure](documentdb-create-documentdb-website.md)
### [Вход в приложения с помощью Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
### [Привязка к функциям Azure](../azure-functions/functions-bindings-documentdb.md)
### [Анализ данных с использованием Hadoop](documentdb-run-hadoop-with-hdinsight.md)
### [Интеграция с Поиском Azure](../search/search-howto-index-documentdb.md)
### [Перенос данных с помощью фабрики данных Azure](../data-factory/data-factory-azure-documentdb-connector.md)
### [Анализ данных в реальном времени с помощью Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md#documentdb)
### [Получение измененной записи HL7 FHIR с помощью Logic Apps](documentdb-change-feed-hl7-fhir-logic-apps.md)
### [Обработка данных, поступающих от датчиков, в реальном времени](../hdinsight/hdinsight-storm-iot-eventhub-documentdb.md)
### [Визуализация данных с помощью Power BI](documentdb-powerbi-visualize.md)
### [Визуализация данных с помощью драйвера ODBC](documentdb-nosql-odbc-driver.md)


# Справочные материалы
## [Java](documentdb-sdk-java.md)
## [.NET](documentdb-sdk-dotnet.md)
## [.NET Core](documentdb-sdk-dotnet-core.md)
## [Node.js](documentdb-sdk-node.md)
## [Python](documentdb-sdk-python.md)
## [REST](/rest/api/documentdb/)
## [Поставщик ресурсов REST](/rest/api/documentdbresourceprovider/)

# Сопутствующие материалы
## [Примеры для .NET](documentdb-dotnet-samples.md)
## [Примеры для Node.js](documentdb-nodejs-samples.md)
## [Примеры для Python](documentdb-python-samples.md)
## [Синтаксис SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
## [Памятка по грамматике SQL](documentdb-sql-query-cheat-sheet.md)

# Ресурсы
## [Цены](https://azure.microsoft.com/pricing/details/documentdb/)
## [Форум MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=documentdb)
## [Обновления службы](https://azure.microsoft.com/updates/?product=documentdb)
## [Портал сообщества](documentdb-community.md)
## [Query Playground](https://www.documentdb.com/sql/demo)
## [Документ по индексированию без схемы](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)
## [Объяснение согласованности данных на примере игры в бейсбол](http://research.microsoft.com/apps/pubs/default.aspx?id=206913)
## [Книга: "Using Microsoft Azure DocumentDB in a Node.js Application" (Использование Microsoft Azure DocumentDB в приложении Node.js)](https://go.microsoft.com/fwlink/?LinkId=828428&clcid=0x409)
## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/documentdb/)


<!--HONumber=Feb17_HO2-->


