# [Документация по Data Lake Store](index.md)

# Обзор
## [Обзор Azure Data Lake Store](data-lake-store-overview.md)
## [Сравнение Azure Data Lake Store со службой хранилища Azure](data-lake-store-comparison-with-blob-storage.md)
## [Azure Data Lake Store для обработки больших данных](data-lake-store-data-scenarios.md)
## [Приложения с открытым кодом, совместимые с Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
## [Рекомендации по использованию Data Lake Store](data-lake-store-best-practices.md)

# Начало работы
## [Использование портала](data-lake-store-get-started-portal.md)
## [PowerShell](data-lake-store-get-started-powershell.md)
## [Использование Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)


# Практическое руководство
## Загрузка и перемещение данных
### [Использование фабрики данных Azure](../data-factory/load-azure-data-lake-store.md)
### [Использование Обозревателя службы хранилища](data-lake-store-in-storage-explorer.md)
### [С помощью AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [С помощью DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Использование Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Передача данных из источников вне сети](data-lake-store-offline-bulk-data-upload.md)
### [Перенос хранилища Azure Data Lake Store между регионами](data-lake-store-migration-cross-region.md)

## Защита данных
### [Обзор безопасности](data-lake-store-security-overview.md)
### [Контроль доступа в Data Lake Store](data-lake-store-access-control.md)
### [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
### [Шифрование](data-lake-store-encryption.md)

## Аутентификация с использованием Data Lake Store
### [Параметры аутентификации](data-lakes-store-authentication-using-azure-active-directory.md)
### [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [С использованием Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Использование пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Использование REST API](data-lake-store-end-user-authenticate-rest-api.md)
#### [Использование Python](data-lake-store-end-user-authenticate-python.md)
### [Аутентификация между службами](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [С использованием Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Использование пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Использование REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Использование Python](data-lake-store-service-to-service-authenticate-python.md)

## Работа с Data Lake Store
### Операции управления учетными записями
#### [Использование пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
#### [Использование REST API](data-lake-store-get-started-rest-api.md)
#### [Использование Python](data-lake-store-get-started-python.md)
### Операции файловой системы
#### [Использование пакета SDK для .NET](data-lake-store-data-operations-net-sdk.md)
#### [Использование пакета SDK для Java](data-lake-store-get-started-java-sdk.md)
#### [Использование REST API](data-lake-store-data-operations-rest-api.md)
#### [Использование Python](data-lake-store-data-operations-python.md)

## Производительность
### [Рекомендации по настройке производительности Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
### [Рекомендации по настройке производительности для использования PowerShell с Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
### [Рекомендации по настройке производительности для Spark в HDInsight и Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
### [Рекомендации по настройке производительности для Hive в HDInsight и Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
### [Рекомендации по настройке производительности для MapReduce в HDInsight и Azure Data Lake Store](data-lake-store-performance-tuning-mapreduce.md)
### [Рекомендации по настройке производительности для Storm в HDInsight и Azure Data Lake Store](data-lake-store-performance-tuning-storm.md)

## Интеграция со службами Azure
### Использование HDInsight
#### [Использование портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Использование Azure PowerShell (хранилище по умолчанию)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Использование Azure PowerShell (дополнительное хранилище)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Использование шаблона Azure](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Доступ с виртуальных машин в виртуальной сети Azure](data-lake-store-connectivity-from-vnets.md)
### [Использование с Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Использование концентраторов событий Azure](data-lake-store-archive-eventhub-capture.md)
### [Использование с фабрикой данных](../data-factory/load-azure-data-lake-store.md)
### [Использование со Stream Analytics](data-lake-store-stream-analytics.md)
### [Использование с Power BI](data-lake-store-power-bi.md)
### [Использование с каталогом данных](data-lake-store-with-data-catalog.md)
### [Использование элементов в хранилище данных SQL с помощью PolyBase](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Использование с SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Другие варианты интеграции с Azure](data-lake-store-integrate-with-other-services.md)

## управление
### [Доступ к журналам диагностики](data-lake-store-diagnostic-logs.md)
### [Планирование высокой доступности](data-lake-store-disaster-recovery-guidance.md)

# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (управление учетными записями)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
## [Python (управление файловой системой)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/dls)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/)
## [Блог Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Отправка отзывов с помощью UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Цены](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Форум Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
