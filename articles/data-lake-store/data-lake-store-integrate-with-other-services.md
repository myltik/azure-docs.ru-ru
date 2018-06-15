---
title: Интеграция Data Lake Store с другими службами Azure | Документация Майкрософт
description: Принципы интеграции хранилища озера данных с другими службами Azure
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 3fd1b03d2ce0b814f453ae4d87a136f28479662d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624103"
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Интеграция хранилища озера данных с другими службами Azure
Хранилище озера данных Azure можно использовать вместе с другими службами Azure для охвата более широкого диапазона сценариев. В следующей статье перечислены службы, с которыми может интегрироваться хранилище озера данных.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Использование хранилища озера данных с Azure HDInsight
Можно подготовить кластер [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) , который использует хранилище озера данных в качестве хранилища, соответствующего требованиям HDFS. В этом выпуске для кластеров Hadoop и Storm в Windows и Linux можно использовать хранилище озера данных только в качестве дополнительного. В этих кластерах по-прежнему используется хранилище Azure (WASB) в качестве хранилища по умолчанию. Тем не менее для кластеров HBase в Windows и Linux можно использовать хранилище озера данных как хранилище по умолчанию, дополнительное хранилище или и то и другое.

Инструкции по подготовке кластера HDInsight с хранилищем озера данных см. в следующих разделах.

* [Подготовка кластера HDInsight и хранилища озера данных с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Подготовка кластера HDInsight с использованием Data Lake Store (как хранилища по умолчанию) и Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Подготовка кластера HDInsight с использованием Data Lake Store (как дополнительного хранилища) и Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Использование хранилища озера данных с аналитикой озера данных Azure
[Аналитика озера данных Azure](../data-lake-analytics/data-lake-analytics-overview.md) позволяет работать с большими данными в облачных масштабах. Она динамически подготавливает ресурсы и позволяет выполнять аналитические операции с терабайтами и даже эксабайтами данных, которые могут храниться в разных поддерживаемых источниках данных, одним из которых является хранилище озера данных. Аналитика озера данных оптимизирована для работы с хранилищем озера данных Azure, благодаря чему обеспечивается наивысший уровень производительности, пропускной способности и параллелизации для рабочих нагрузок больших данных.

Инструкции по использованию Data Lake Analytics с Data Lake Store см. в разделе [Начало работы с аналитикой озера данных с использованием хранилища озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Использование хранилища озера данных с фабрикой данных Azure
Можно использовать [фабрику данных Azure](https://azure.microsoft.com/services/data-factory/) для приема данных из таблиц Azure, Базы данных SQL Azure, хранилища данных SQL Azure, больших двоичных объектов хранилища Azure и локальных баз данных. Фабрика данных Azure является ведущим компонентом экосистемы Azure, ее можно использовать для координации приема данных из этих источников в хранилище озера данных Azure.

Инструкции по использованию фабрики данных Azure c Data Lake Store см. в разделе [Перемещение данных из хранилища озера данных и обратно с использованием фабрики данных](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных
Хранилище озера данных Azure предоставляет средство командной строки AdlCopy для копирования данных из хранилища больших двоичных объектов Azure в учетную запись хранилища озера данных. Дополнительные сведения см. в статье [Копирование данных из больших двоичных объектов службы хранилища Azure в Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Копирования данных между базой данных SQL Azure и хранилищем озера данных
Можно использовать Apache Sqoop для импорта и экспорта данных между базой данных SQL Azure и хранилищем озера данных. Дополнительные сведения см. в статье [Копирование данных между хранилищем озера данных и базой данных SQL Azure с помощью Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Использование хранилища озера данных со Stream Analytics
Хранилище озера данных можно использовать для сохранения потока выходных данных с помощью Azure Stream Analytics. Дополнительные сведения см. в разделе [Потоковая передача данных из большого двоичного объекта службы хранилища Azure в хранилище озера данных с помощью Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Использование хранилища озера данных с Power BI
Power BI можно использовать, чтобы импортировать данные из учетной записи хранилища озера данных для анализа и визуализации. Дополнительные сведения см. в разделе [Анализ данных в хранилище озера данных с помощью Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Использование хранилища озера данных с каталогом данных
Данные из хранилища озера данных можно зарегистрировать в каталоге данных Azure, чтобы упростить их обнаружение в организации. Дополнительные сведения см. в разделе [Регистрация данных из хранилища озера данных в каталоге данных Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Использование Data Lake Store со службами SQL Server Integration Services (SSIS)
Вы можете использовать диспетчер подключений Azure Data Lake Store в SSIS для подключения пакета служб SSIS к Azure Data Lake Store. См. дополнительные сведения об [использовании Data Lake Store со службами SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Использование Data Lake Store с хранилищем данных SQL
С помощью PolyBase вы можете загружать данные из Azure Data Lake Store в хранилище данных SQL. См. дополнительные сведения об [использовании Data Lake Store с хранилищем данных SQL](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Использование Data Lake Store с концентраторами событий Azure
Azure Data Lake Store можно использовать для архивации и записи данных, полученных концентраторами событий Azure. Дополнительные сведения см. в статье [Use Azure Data Lake Store to capture data from Event Hubs](data-lake-store-archive-eventhub-capture.md) (Запись данных из концентраторов событий с помощью Azure Data Lake Store).

## <a name="see-also"></a>См. также
* [Обзор Azure Data Lake Store](data-lake-store-overview.md)
* [Начало работы с хранилищем озера данных с помощью портала](data-lake-store-get-started-portal.md)
* [Начало работы с хранилищем озера данных с помощью PowerShell](data-lake-store-get-started-powershell.md)  

