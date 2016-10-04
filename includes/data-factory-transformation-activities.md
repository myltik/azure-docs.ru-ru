Фабрика данных Azure поддерживает указанные ниже действия преобразования, которые вы можете добавлять в конвейеры как по отдельности, так и в связи с другим действием.

Действия по преобразованию данных | Вычислительная среда 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop] [Потоковая передача Hadoop](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Действия машинного обучения: выполнение пакета и обновление ресурса](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure 
[Хранимая процедура](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL, хранилище данных Azure SQL или SQL Server |
[Аналитика озера данных U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Аналитика озера данных Azure 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] или пакетная служба Azure
   
> [AZURE.NOTE] 
Действие MapReduce можно использовать для запуска программ Spark в кластере HDInsight Spark. Дополнительные сведения см. в статье [Вызов программ Spark из фабрики данных](../articles/data-factory/data-factory-spark.md). Можно создать настраиваемое действие для выполнения сценариев R в кластере HDInsight, где установлена среда R. См. пример в репозитории Github [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Запуск сценария R с помощью фабрики данных Azure).

<!---HONumber=AcomDC_0928_2016-->