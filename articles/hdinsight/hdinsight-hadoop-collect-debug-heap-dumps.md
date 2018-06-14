---
title: Отладка и анализ служб Hadoop с помощью дампов кучи в Azure | Документы Майкрософт
description: Автоматически собирайте дампы кучи для служб Hadoop и размещайте их в учетной записи хранения BLOB-объектов Azure для отладки и анализа.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0721d20987008e5cc6370c6e853440ce93edcfa9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398124"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Сбор дампов кучи в хранилище больших двоичных объектов для отладки и анализа служб Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Дампы кучи содержат снимок памяти приложения, включая значения переменных на момент создания дампа. Поэтому они полезны для диагностики проблем, возникающих во время выполнения. Сборка дампов кучи для служб Hadoop может выполняться автоматически, после чего они помещаются в учетную запись хранилища больших двоичных объектов Azure в раздел HDInsightHeapDumps/.

Сбор дампов кучи для различных служб нужно включать на отдельных кластерах. По умолчанию эта функция отключена. Размер дампов кучи может быть большим, поэтому мы советуем следить за учетной записью хранилища больших двоичных объектов после включения сбора.

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement). Сведения в этой статье применимы только к HDInsight, работающей под управлением Windows. Сведения о HDInsight под управлением Linux см. в статье [Включение дампов кучи для служб Hadoop в HDInsight, работающей под управлением Linux (предварительная версия)](hdinsight-hadoop-collect-debug-heap-dump-linux.md).


## <a name="eligible-services-for-heap-dumps"></a>Службы с возможностью включения дампов кучи
Вы можете включить дампы кучи для следующих служб:

* **Hcatalog** — tempelton;
* **Hive** — hiveserver2, metastore, derbyserver;
* **Mapreduce** — jobhistoryserver;
* **Yarn** — resourcemanager, nodemanager, timelineserver;
* **HDFS** — datanode, secondarynamenode, namenode.

## <a name="configuration-elements-that-enable-heap-dumps"></a>Элементы конфигурации, активирующие дампы кучи
Чтобы включить дампы кучи для службы, необходимо задать необходимые элементы конфигурации в разделе для этой службы, обозначенном аргументом **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Значением **service_name** может быть любая из следующих служб: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode или namenode.

## <a name="enable-using-azure-powershell"></a>Включить при помощи Azure PowerShell
Например, чтобы включить дампы кучи с помощью Azure PowerShell для jobhistoryserver, можно использовать следующий скрипт:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Включить при помощи пакета SDK для .NET
Например, чтобы включить дампы кучи с помощью пакета SDK .NET для Azure HDInsight для jobhistoryserver, можно использовать следующий код:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
