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
	ms.date="10/13/2015" 
	ms.author="spelluru"/>

# Фабрика данных Azure — журнал изменений пакета SDK для .NET 
В этой статье содержится информация об изменениях в определенной версии пакета SDK для фабрики данных Azure. Последнюю версию пакета NuGet для фабрики данных Azure можно найти [здесь](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

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
    
- **Версия API** для этого выпуска: **2015-10-01**.

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

### Добавление функций
- Класс [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) поддерживает два новых свойства **SliceIdentifierColumnName** и **SqlWriterCleanupScript** для поддержки идемпотентного копирования в хранилище данных SQL Azure. См. статью [Хранилище данных SQL Azure](data-factory-azure-sql-data-warehouse-connector.md), в частности разделы [Механизм 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) и [Механизм 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) для получения подробных сведений об этих свойствах.

- Теперь поддерживаются запуск хранимой процедуры для базы данных SQL Azure и источников хранилищ данных SQL в ходе операции копирования. Классы [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) и [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) имеют следующие свойства для поддержки этой возможности: **SqlReaderStoredProcedureName** и **StoredProcedureParameters**. Подробные сведения об этих свойствах см. в статьях [Базы данных SQL Azure](data-factory-azure-sql-connector.md#sqlsource) и [Хранилище данных SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) на сайте Azure.com.

<!---HONumber=Oct15_HO3-->