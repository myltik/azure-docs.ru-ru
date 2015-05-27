<properties 
	pageTitle="Сборка дампов кучи для отладки и анализа| Azure" 
	description="Сборка дампов кучи для отладки и анализа" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Сборка дампов кучи для отладки и анализа

Сборка дампов кучи для служб Hadoop может выполняться автоматически, после чего они помещаются в учетную запись хранилища больших двоичных объектов Azure в раздел HDInsightHeapDumps/. Файлы дампа для службы с кучами содержат снимок памяти приложения. В нем содержатся значения переменных во время создания дампа.

Сбор дампов кучи для различных служб нужно включать на отдельных кластерах. По умолчанию эта функция отключена. Размер дампов кучи может быть большим, поэтому мы советуем следить за учетной записью хранилища больших двоичных объектов после включения сбора.


## <a name="whichServices"></a>Службы с возможностью включения дампов кучи

Службы, для которых при необходимости можно включить дампы кучи:

*  **Hcatalog** — tempelton;
*  **Hive** — hiveserver2, metastore, derbyserver; 
*  **Mapreduce** — jobhistoryserver; 
*  **Yarn** — resourcemanager, nodemanager, timelineserver; 
*  **HDFS** — datanode, secondarynamenode, namenode.

## <a name="configuration"></a>Элементы конфигурации, активирующие дампы кучи

Чтобы включить дампы кучи для службы, необходимо задать необходимые элементы конфигурации в разделе для этой службы, обозначенном аргументом **service_name**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

Значением **service_name** может быть любая из указанных выше служб: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode или namenode.

## <a name="powershell"></a>Как включить дампы кучи с помощью Azure PowerShell

Например, чтобы включить дампы кучи с помощью Azure PowerShell для jobhistoryserver, выполните следующее:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Как включить дампы кучи с помощью пакета SDK для Azure для HDInsight .NET

Например, чтобы включить дампы кучи с помощью пакета SDK для Azure для HDInsight .NET для jobhistoryserver, выполните следующее:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));




<!--HONumber=54-->