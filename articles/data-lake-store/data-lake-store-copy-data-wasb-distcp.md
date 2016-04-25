<properties
   pageTitle="Копирование данных из WASB в хранилище озера данных и обратно с помощью Distcp| Microsoft Azure"
   description="Использование средства Distcp для копирования данных из BLOB-объектов хранилища Azure в хранилище озера данных и обратно"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Использование Distcp для копирования данных между BLOB-объектами и хранилищем озера данных

После создания кластера HDInsight, который имеет доступ к учетной записи хранилища озера данных, можно использовать средства экосистемы Hadoop, такие как Distcp, для копирования данных в ** из хранилища кластера HDInsight \(WASB\) в учетную запись хранилища озера данных и обратно**. В этой статье описано, как это сделать.

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).
- **Кластер Azure HDInsight** с доступом к учетной записи хранилища озера данных. См. статью [Создание кластера HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md). Убедитесь, что вы включили удаленный рабочий стол для кластера.

## Использование Distcp на удаленном рабочем столе \(кластер Windows\) или через SSH \(кластер Linux\)

В состав кластера HDInsight входит служебная программа Distcp, которую можно использовать для копирования данных из различных источников в кластер HDInsight. При настройке кластера HDInsight для использования хранилища озера данных в качестве дополнительного хранилища служебную программу Distcp можно использовать для копирования данных в учетную запись хранилища озера данных и из нее без дополнительной настройки. В этом разделе мы рассмотрим, как использовать служебную программу Distcp.

1. Если у вас кластер Windows, удаленно подключитесь к кластеру HDInsight, у которого есть доступ к учетной записи хранилища озера данных. Инструкции см. в подразделе [Подключение к кластерам по протоколу RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Откройте командную строку Hadoop на рабочем столе кластера.

	Если у вас кластер Linux, используйте протокол SSH для подключения к кластеру. См. подраздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Выполните команды в командной строке SSH.

3. Проверьте, доступны ли вам BLOB-объекты хранилища Azure \(WASB\). Выполните следующую команду:

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	Она должна вывести список содержимого в хранилище BLOB-объектов.

4. Аналогичным образом проверьте, доступна ли учетная запись хранилища озера данных из кластера. Выполните следующую команду:

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	Она должна вывести список файлов и папок в учетной записи хранилища озера данных.

5. Используйте Distcp для копирования данных из WASB в учетную запись хранилища озера данных.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	Она скопирует содержимое папки **/example/data/gutenberg/** WASB в папку **/myfolder** в учетной записи хранилища озера данных.

6. Аналогичным образом используйте Distcp для копирования данных из учетной записи хранилища озера данных в WASB.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	Она скопирует содержимое папки **/myfolder** в учетной записи хранилища озера данных в папку **/example/data/gutenberg/** в WASB.

## См. также

- [Copy data from Azure Storage Blobs to Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0413_2016-->