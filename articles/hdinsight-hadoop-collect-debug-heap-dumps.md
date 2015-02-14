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
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# Сборка дампов кучи для отладки и анализа

Сборка дампов кучи для служб Hadoop может выполняться автоматически, после чего они помещаются в учетную запись хранения BLOB в раздел HDInsightHeapDumps/.  Файлы дампа для службы с кучами содержат снимок памяти приложения. В нем содержатся значения переменных во время создания дампа.

Сбор дампов кучи для различных служб нужно включать на отдельных кластерах. По умолчанию эта функция отключена. Размер дампов кучи может быть большим, поэтому мы рекомендуем следить за учетной записью хранения BLOB после включения сбора.

## Содержание

- [Для каких служб можно включить дампы кучи?](#whichServices)
- [Элементы конфигурации, активирующие дампы кучи](#configuration)
- [Как включить дампы кучи с помощью Azure HDInsight PowerShell](#powershell)
- [Как включить дампы кучи с помощью пакета SDK HDInsight .NET](#sdk)


## <a name="whichServices"></a>Для каких служб можно включить дампы кучи?

Службы, для которых при необходимости можно включить дампы кучи: 

*  **HCatalog**: tempelton
*  **Hive**: hiveserver2, metastore, derbyserver 
*  **MapReduce**: jobhistoryserver 
*  **YARN**: resourcemanager, nodemanager, timelineserver 
*  **HDFS**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Элементы конфигурации, активирующие дампы кучи

Чтобы включить дампы кучи для службы, пользователь должен задать необходимые элементы конфигурации в разделе для этой службы, обозначенном аргументом service_name.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

Значением <service_name> может быть любая из указанных выше служб: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode или namenode.

## <a name="powershell"></a>Как включить дампы кучи с помощью Azure HDInsight PowerShell

Например, чтобы включить дампы кучи с помощью PowerShell для jobhistoryserver, выполните следующие действия:

С помощью пакета SDK PowerShell:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Включение дампов кучи с помощью пакета SDK Azure HDInsight .NET

Например, чтобы включить дампы кучи с помощью пакета SDK .NET для jobhistoryserver, выполните следующие действия:

С помощью пакета SDK C#:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



<!--HONumber=42-->
