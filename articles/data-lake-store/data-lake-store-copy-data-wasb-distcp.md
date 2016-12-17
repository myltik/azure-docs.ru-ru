---
title: "Копирование данных из WASB в Data Lake Store и обратно с помощью Distcp | Документация Майкрософт"
description: "Использование средства Distcp для копирования данных из BLOB-объектов хранилища Azure в хранилище озера данных и обратно"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: dcab09385c6a664186d124ce3e042cc1fac82bd6


---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Использование Distcp для копирования данных между BLOB-объектами и хранилищем озера данных
> [!div class="op_single_selector"]
> * [С помощью DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [С помощью AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Создав кластер HDInsight с доступом к учетной записи Data Lake Store, вы можете использовать такие средства экосистемы Hadoop, как Distcp, для **копирования** данных из хранилища кластера HDInsight (WASB) в учетную запись хранилища озера данных и обратно. В этой статье описано, как это сделать.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Настройте свою подписку Azure** для использования общедоступной предварительной версии Data Lake Store. Ознакомьтесь с [инструкциями](data-lake-store-get-started-portal.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Store. См. статью [Создание кластера HDInsight с Data Lake Store с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Убедитесь, что вы включили удаленный рабочий стол для кластера.

## <a name="do-you-learn-fast-with-videos"></a>Учитесь быстрее с помощью видео?
[Просмотрите это видео](https://mix.office.com/watch/1liuojvdx6sie) об использовании Distcp для копирования данных между большими двоичными объектами службы хранилища Azure и Data Lake Store.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Использование Distcp на удаленном рабочем столе (кластер Windows) или через SSH (кластер Linux)
В состав кластера HDInsight входит служебная программа Distcp, которую можно использовать для копирования данных из различных источников в кластер HDInsight. При настройке кластера HDInsight для использования хранилища озера данных в качестве дополнительного хранилища служебную программу Distcp можно использовать для копирования данных в учетную запись хранилища озера данных и из нее без дополнительной настройки. В этом разделе мы рассмотрим, как использовать служебную программу Distcp.

1. Если у вас кластер Windows, удаленно подключитесь к кластеру HDInsight, у которого есть доступ к учетной записи хранилища озера данных. Инструкции см. в разделе [Подключение к кластерам по протоколу RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Откройте командную строку Hadoop на рабочем столе кластера.

    Если у вас кластер Linux, используйте протокол SSH для подключения к кластеру. См. раздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Выполните команды в командной строке SSH.
2. Проверьте, доступны ли вам BLOB-объекты хранилища Azure (WASB). Выполните следующую команду:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Она должна вывести список содержимого в хранилище BLOB-объектов.
3. Аналогичным образом проверьте, доступна ли учетная запись хранилища озера данных из кластера. Выполните следующую команду:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Она должна вывести список файлов и папок в учетной записи хранилища озера данных.
4. Используйте Distcp для копирования данных из WASB в учетную запись хранилища озера данных.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Эта команда скопирует содержимое папки **/example/data/gutenberg/** в WASB в папку **/myfolder** в учетной записи Data Lake Store.
5. Аналогичным образом используйте Distcp для копирования данных из учетной записи хранилища озера данных в WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Эта команда скопирует содержимое папки **/myfolder** в учетной записи Data Lake Store в папку **/example/data/gutenberg/** в WASB.

## <a name="see-also"></a>Дополнительные материалы
* [Copy data from Azure Storage Blobs to Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


