---
title: "Сценарии работы с данными с использованием Data Lake Store | Документация Майкрософт"
description: "Сведения о различных сценариях и средствах для приема, обработки, загрузки и визуализации данных в хранилище озера данных"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 15460ef4add0e0ab94e776ffb715b86d39b89659


---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Использование хранилища озера данных Azure для потребностей больших данных
Процесс обработки больших данных состоит из указанных далее четырех ключевых этапов.

* Прием больших объемов данных в хранилище данных в режиме реального времени или в пакетах
* Обработка данных
* Загрузка данных
* Визуализация данных

В этой статье мы рассмотрим эти этапы применительно к хранилищу озера данных Azure, чтобы понять параметры и средства, доступные для удовлетворения потребностей в обработке больших объемов данных.

## <a name="ingest-data-into-data-lake-store"></a>Прием данных в хранилище озера данных
В этом разделе описываются различные источники данных и способы поступления этих данных в учетную запись хранения озера данных.

![Прием данных в хранилище озера данных](./media/data-lake-store-data-scenarios/ingest-data.png "Ingest data into Data Lake Store")

### <a name="ad-hoc-data"></a>Специальные данные
Это небольшие наборы данных, которые используются для создания прототипов приложений для работы с большими данными. В зависимости от источника данных применяются разные способы приема специальных данных.

| Источник данных | Средство для приема |
| --- | --- |
| Локальный компьютер |<ul> <li>[Портал Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Межплатформенный интерфейс командной строки Azure](data-lake-store-get-started-cli.md)</li> <li>[Data Lake Tools для Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Большой двоичный объект хранилища Azure |<ul> <li>[Фабрика данных Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[инструмента AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp, запущенный на кластере HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Потоковые данные
Это данные, создаваемые различными источниками, такими как приложения, устройства, датчики и т. д. Для ввода этих данных в хранилище озера данных можно использовать множество средств. Как правило, эти средства собирают и обрабатывают данные на основе событий в режиме реального времени и затем записывают события в пакетном режиме в хранилище озера данных для последующей обработки.

Ниже перечислены средства, которые можно использовать:

* [Azure Stream Analytics.](../stream-analytics/stream-analytics-data-lake-output.md) События, принятые в концентраторы событий, могут записываться в Azure Data Lake с помощью выходных данных Azure Data Lake Store.
* [Azure HDInsight Storm.](../hdinsight/hdinsight-storm-write-data-lake-store.md) Данные из кластера Storm можно напрямую записывать в Data Lake Store.
* [EventProcessorHost.](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) Можно получать события из концентраторов событий, а затем записывать их в Data Lake Store с помощью [пакета SDK для .NET Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Реляционные данные
Можно также извлекать данные из реляционных баз данных. В течение определенного периода времени реляционные базы данных собирают огромные объемы данных, которые после обработки с помощью конвейера больших данных могут предоставлять ценные сведения. Для перемещения таких данных в хранилище озера данных можно использовать следующие средства.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Фабрика данных Azure](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Данные журналов веб-сервера (отправка с помощью настраиваемых приложений)
Этот тип набора данных вызывается специально, так как анализ данных журналов веб-сервера часто используется в приложениях по работе с большими данными, и для его выполнения требуется отправка больших объемов файлов журналов в хранилище озера данных. Для отправки таких данных воспользуйтесь следующими средствами или напишите собственные сценарии или приложения.

* [Межплатформенный интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Пакет SDK для .NET хранилища озера данных Azure](data-lake-store-get-started-net-sdk.md)
* [Фабрика данных Azure](../data-factory/data-factory-data-movement-activities.md)

Отличным способом отправки данных журналов веб-сервера и других типов данных (например данных общественных мнений) является использование собственных написанных сценариев или приложений, поскольку вы можете включить компонент отправки данных в состав более масштабного приложения по работе с большими объемами данных. В одних случаях этот код может иметь форму сценария или простой программы командной строки. В других случаях код может использоваться для интеграции обработки больших данных в бизнес-приложение или решение.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Данные, связанные с кластерами HDInsight Azure
Большинство типов кластера HDInsight (Hadoop, HBase, Storm) поддерживают хранилище озера данных в качестве репозитория хранения данных. Кластеры HDInsight обращаются к данным из BLOB-объектов хранилища Azure (WASB). Для повышения производительности можно скопировать данные из WASB в учетную запись хранения озера данных, связанную с кластером. Для копирования данных можно использовать указанные далее средства.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Служба AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Фабрика данных Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Данные, хранящиеся в локальных кластерах Hadoop или кластерах Hadoop в IaaS
Большие объемы данных могут храниться в кластерах Hadoop, размещенных локально на компьютерах, использующих HDFS. Кластеры Hadoop могут быть развернуты локально или могу работать в кластере IaaS в Azure. К копированию таких данных в хранилище озера данных Azure могут предъявляться требования, в зависимости от того, является ли эта операция одноразовой или повторяющейся. Существуют различные возможности выполнить их. Ниже приведен список альтернативных вариантов и связанные с ними компромиссы.

| Подход | Сведения | Преимущества | Рекомендации |
| --- | --- | --- | --- |
| Использование фабрики данных Azure (ADF) для копирования данных напрямую из кластеров Hadoop в хранилище озера данных Azure. |[ADF поддерживает HDFS в качестве источника данных.](../data-factory/data-factory-hdfs-connector.md) |ADF реализована готовая поддержка HDFS, а также первоклассные инструменты комплексного управления и мониторинга. |Требуется развернуть шлюз управления данными в локальном кластере или кластере IaaS. |
| Экспорт данных из Hadoop в виде файлов. Затем — копирование этих файлов в хранилище озера данных Azure с помощью соответствующего механизма. |Скопировать файлы в Azure Data Lake Store можно с помощью:  <ul><li>[Azure PowerShell только для Windows](data-lake-store-get-started-powershell.md)</li><li>[Кроссплатформенный интерфейс командной строки Azure для ОС, отличных от Windows](data-lake-store-get-started-cli.md)</li><li>Пользовательское приложение, использующее любой пакет SDK Data Lake Store</li></ul> |Можно быстро приступить к работе. Возможны настраиваемые передачи данных. |Многоэтапный процесс с использованием нескольких технологий. Учитывая настраиваемый характер инструментов, со временем будет все сложнее осуществлять управление и мониторинг. |
| Использование Distcp для копирования данных из Hadoop в службу хранилища Azure. Затем — копирование данных из службы хранилища Azure в хранилище озера данных с помощью соответствующего механизма. |Скопировать данные из службы хранилища Azure в Data Lake Store можно с помощью:  <ul><li>[Фабрика данных Azure](../data-factory/data-factory-data-movement-activities.md)</li><li>[инструмента AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp, запущенный в кластерах HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Можно использовать инструменты с открытым кодом. |Многоэтапный процесс с использованием нескольких технологий. |

### <a name="really-large-datasets"></a>Очень большие наборы данных
Для отправки наборов данных размером в несколько терабайт использование описанных выше методов иногда может быть медленным и затратным процессом. В таких ситуациях будут уместны следующие варианты.

* **Использование Azure ExpressRoute.** Azure ExpressRoute позволяет создавать закрытые соединения между ЦОД Azure и вашей локальной инфраструктурой. Это надежный вариант для передачи больших объемов данных. Дополнительные сведения см. в [техническом обзоре ExpressRoute](../expressroute/expressroute-introduction.md).
* **Автономная передача данных**. Если по какой-то причине использовать Azure ExpressRoute нельзя, используйте [службу импорта и экспорта Azure](../storage/storage-import-export-service.md) для доставки жестких дисков с данными в центр обработки данных Azure. Данные сначала будут отправлены в хранилище BLOB-объектов Azure. Затем с помощью [фабрики данных Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) или [инструмента AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) можно скопировать данные из больших двоичных объектов хранилища Azure в Data Lake Store.

  > [!NOTE]
  > При использовании службы импорта и экспорта размеры файлов на дисках, отправляемых в ЦОД Azure, не должны превышать 200 ГБ.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Обработка данных, хранящихся в хранилище озера данных 
Данные, доступные в хранилище озера данных, можно проанализировать с помощью поддерживаемых приложений для работы с большими данными. В настоящее время для запуска заданий анализа для данных, хранящихся в хранилище озера данных, можно использовать Azure HDInsight и аналитику озера данных Azure.

![Анализ данных в хранилище озера данных](./media/data-lake-store-data-scenarios/analyze-data.png "Analyze data in Data Lake Store")

Рассмотрите следующие примеры.

* [Создание кластера HDInsight с хранилищем озера данных в качестве хранилища](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Загрузка данных из хранилища озера данных
Возможно, вам потребуется загрузить или переместить данные из хранилища озера данных Azure в случаях, аналогичных указанным далее.

* Перемещение данных в другие репозитории для взаимодействия с существующими конвейерами обработки данных. Например, можно переместить данные из хранилища озера данных в базу данных SQL Azure или на локальный сервер SQL Server.
* Загрузка данных на локальный компьютер для обработки в средах IDE при создании прототипов приложений.

![Вывод данных из хранилища озера данных](./media/data-lake-store-data-scenarios/egress-data.png "Egress data from Data Lake Store")

В таких ситуациях можно использовать любой из следующих вариантов:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Фабрика данных Azure](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Для написания сценария или приложения для загрузки данных из хранилища озера данных можно воспользоваться следующими средствами.

* [Межплатформенный интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Пакет SDK для .NET хранилища озера данных Azure](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Визуализация данных в хранилище озера данных
Для создания визуальных представлений данных, хранящихся в хранилище озера данных, можно использовать сочетание служб.

![Визуализация данных в хранилище озера данных](./media/data-lake-store-data-scenarios/visualize-data.png "Visualize data in Data Lake Store")

* Сначала с помощью [фабрики данных Azure переместите данные из Data Lake Store в хранилище данных SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats).
* После этого вы можете [интегрировать Power BI с хранилищем данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) для создания визуального представления данных.



<!--HONumber=Nov16_HO3-->


