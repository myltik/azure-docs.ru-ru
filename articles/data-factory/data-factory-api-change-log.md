<properties 
	pageTitle="Фабрика данных — журнал изменений API для .NET | Microsoft Azure" 
	description="Описываются критические изменения, добавления функций, исправления ошибок и т. д. — для определенной версии API для .NET для фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/05/2016" 
	ms.author="spelluru"/>

# Фабрика данных Azure — журнал изменений в .NET API 
В этой статье содержится информация об изменениях в определенной версии пакета SDK для фабрики данных Azure. Последнюю версию пакета NuGet для фабрики данных Azure можно найти [здесь](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

## Версия 4.7.0
Дата выпуска: 20 мая 2016 г.

### Добавление функций
* Добавлен новый тип StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) для копирования файлов в формате столбцов с оптимизацией по строкам (ORC).
* Добавлены свойства [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) и PolyBaseSettings в SqlDWSink.
    * Позволяет использовать PolyBase для копирования данных в хранилище данных SQL.

## Версия 4.6.1
Дата выпуска: 26 апреля 2016 г.

### Исправления ошибок
* Исправляет HTTP-запрос для получения списка действий Windows.
    * Удаляет имя группы ресурсов и имя фабрики данных из полезных данных запроса.

## Версия 4.6.0
Дата выпуска: 14.04.2016

### Добавление функций

- В раздел [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx) добавлены следующие свойства:
	- [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
	- [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
	- [Наборы данных](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- В раздел [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx) добавлены следующие свойства:
	- [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Добавлен новый тип [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) — [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) — для определения наборов данных, данные которых представлены в формате JSON.

## Версия 4.5.0
Дата выпуска: 24.02.2016

### Добавление функций
* Добавлен [список операций для окна действий](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Добавлены методы для получения окон действий с фильтрам на основе типов сущностей (т. е. фабрик данных, наборов данных, конвейеров и действий).
* Добавлены следующие типы связанных служб:
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Добавлены следующие типы наборов данных:
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Добавлены следующие типы источников копий:
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## Версия 4.4.0
Дата выпуска: 28.01.2016

### Новые функции

- Для действий копирования в качестве источника и приемника данных добавлен следующий тип связанной службы:
	- [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Основные сведения и примеры см. в статье [Azure Storage SAS Linked Service](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) (Связанная служба SAS хранилища Azure).

## Версия 4.3.0
Дата выпуска: 25.11.2015

### Новые функции

- Для действий копирования в качестве источника данных добавлен следующий тип связанной службы:
	- [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Основные сведения и примеры см. в статье [Move data from HDFS using Data Factory](data-factory-hdfs-connector.md) (Перемещение данных из локальной системы HDFS с помощью фабрики данных Azure).
	- [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Основные сведения и примеры см. в статье [Move data From ODBC data stores using Azure Data Factory](data-factory-odbc-connector.md) (Перемещение данных из хранилищ данных ODBC с помощью фабрики данных Azure).

## Версия 4.2.0
Дата выпуска: 10.11.2015

### Новые функции

- Был добавлен следующий новый тип действия: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Подробные сведения об этом действии см. в статье [Updating Azure ML models using the Update Resource Activity](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").
- Новое необязательное свойство [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) было добавлено в [класс AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
- Свойства [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) и [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) были добавлены в класс [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
- Разрешена настройка времени ожидания для клиентских вызовов в службу фабрики данных.


## Версия 4.1.0
Дата выпуска: 28 октября 2015 г.

### Новые функции
* Добавлены следующие типы связанных служб:
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Добавлены следующие типы действий:
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Добавлены следующие типы наборов данных:
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Добавлены следующие типы источника и приемника для копирования:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## Версия 4.0.1
Дата выпуска: 13.10.2015

### Критические изменения
Следующие классы были переименованы. В качестве новых имен были использованы исходные имена классов до выпуска 4.0.0.
 
Имя в выпуске 4.0.0 | Имя в выпуске 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Версия 4.0.0
Дата выпуска: 02.10.2015

### Критические изменения



- Следующие классы и интерфейсы были переименованы.

| Старое имя | Новое имя |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Таблица | [Выборка](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- **Версия API** для этого выпуска: **1 октября 2015 г.**.

- Методы **List** теперь возвращают результаты с разбивкой на страницы. Если ответ содержит непустое свойство **NextLink**, клиентскому приложению необходимо продолжить извлечение следующей страницы до тех пор, пока не будут возвращены все страницы. Пример:

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- API конвейера **List** возвращает только сводную информацию о конвейере, а не полную информацию. Например, действия в сводной информации о конвейере содержат только имя и тип.

### Новые функции
- Класс [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) поддерживает два новых свойства, **SliceIdentifierColumnName** и **SqlWriterCleanupScript**, для поддержки идемпотентного копирования в хранилище данных SQL Azure. См. статью [Хранилище данных SQL Azure](data-factory-azure-sql-data-warehouse-connector.md), в частности разделы [Механизм 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) и [Механизм 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2), для получения подробных сведений об этих свойствах.

- Теперь поддерживаются запуск хранимой процедуры для базы данных SQL Azure и источников хранилищ данных SQL в ходе операции копирования. Классы [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) и [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) имеют следующие свойства для поддержки этой возможности: **SqlReaderStoredProcedureName** и **StoredProcedureParameters**. Подробные сведения об этих свойствах см. в статьях [Базы данных SQL Azure](data-factory-azure-sql-connector.md#sqlsource) и [Хранилище данных SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) на сайте Azure.com.

<!---HONumber=AcomDC_0706_2016-->