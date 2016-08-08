<properties
	pageTitle="Отладка и анализ служб Hadoop с помощью дампов кучи | Microsoft Azure"
	description="Автоматически собирайте дампы кучи для служб Hadoop и размещайте их в учетной записи хранения BLOB-объектов Azure для отладки и анализа."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


# Сбор дампов кучи в хранилище больших двоичных объектов для отладки и анализа служб Hadoop

[AZURE.INCLUDE [Селектор heapdump](../../includes/hdinsight-selector-heap-dump.md)]

Дампы кучи содержат снимок памяти приложения, включая значения переменных на момент создания дампа. Поэтому они очень полезны для диагностики проблем, возникающих во время выполнения. Сборка дампов кучи для служб Hadoop может выполняться автоматически, после чего они помещаются в учетную запись хранилища больших двоичных объектов Azure в раздел HDInsightHeapDumps/.

Сбор дампов кучи для различных служб нужно включать на отдельных кластерах. По умолчанию эта функция отключена. Размер дампов кучи может быть большим, поэтому мы советуем следить за учетной записью хранилища больших двоичных объектов после включения сбора.

> [AZURE.NOTE] Сведения в этой статье применимы только к HDInsight, работающей под управлением Windows. Сведения об HDInsight, работающей под управлением Linux, см. в статье [Включение дампов кучи для службы Hadoop в HDInsight, работающей под управлением Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## Службы с возможностью включения дампов кучи

Вы можете включить дампы кучи для следующих служб:

*  **Hcatalog** — tempelton;
*  **Hive** — hiveserver2, metastore, derbyserver;
*  **Mapreduce** — jobhistoryserver;
*  **Yarn** — resourcemanager, nodemanager, timelineserver;
*  **HDFS** — datanode, secondarynamenode, namenode.

## Элементы конфигурации, активирующие дампы кучи

Чтобы включить дампы кучи для службы, необходимо задать необходимые элементы конфигурации в разделе для этой службы, обозначенном аргументом **service\_name**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Значением **service\_name** может быть любая из указанных выше служб: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode или namenode.

## Включить при помощи Azure PowerShell

Например, чтобы включить дампы кучи с помощью Azure PowerShell для jobhistoryserver, выполните следующее:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## Включить при помощи пакета SDK для .NET

Например, чтобы включить дампы кучи с помощью пакета SDK для Azure для HDInsight .NET для jobhistoryserver, выполните следующее:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=AcomDC_0727_2016-->