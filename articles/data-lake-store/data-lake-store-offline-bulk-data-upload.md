---
title: Отправка больших объемов данных в Data Lake Store автономными методами | Документация Майкрософт
description: Использование средства AdlCopy для копирования данных из больших двоичных объектов хранилища Azure в Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 2b3ae9e4ecb8b8db4eee109f0867c7884bea37c2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625684"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Использование службы импорта и экспорта Azure для автономного копирования данных в Data Lake Store
В этой статье вы узнаете о том, как скопировать огромные наборы данных (> 200 ГБ) в Azure Data Lake Store, используя методы автономного копирования, такие как [служба импорта и экспорта Azure](../storage/common/storage-import-export-service.md). В частности, в качестве примера в этой статье используется файл размером 339 420 860 416 байт, т. е. 319 ГБ на диске. Давайте назовем этот файл 319GB.tsv.

Служба импорта и экспорта Azure позволяет безопасно переносить большие объемы данных в хранилище BLOB-объектов Azure, отправляя жесткие диски в центр обработки данных Azure.

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы убедитесь, что у вас есть такие компоненты.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранения Azure.**
* **Учетная запись Azure Data Lake Store.** Инструкции по созданию учетной записи см. в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).

## <a name="preparing-the-data"></a>Подготовка данных

Перед использованием службы импорта и экспорта необходимо разделить передаваемый файл данных на **копии, размер которых не превышает 200 ГБ**. Это нужно сделать, так как инструмент для импорта не работает с файлами, размер которых превышает 200 ГБ. В этом руководстве мы разделим файл на блоки по 100 ГБ. Это можно сделать с помощью [Cygwin](https://cygwin.com/install.html). Это средство поддерживает команды Linux. Воспользуйтесь следующей командой:

    split -b 100m 319GB.tsv

Операция Split создает файлы с именами, указанными ниже.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Подготовка дисков с данными
Для подготовки жестких дисков следуйте инструкциям в статье [Использование службы импорта и экспорта Azure для передачи данных в хранилище BLOB-объектов](../storage/common/storage-import-export-service.md) в подразделе **Подготовка дисков**. Вот общая последовательность действий:

1. Приобретите жесткий диск, который отвечает требованиям к использованию в службе импорта и экспорта Azure.
2. Определите учетную запись хранения Azure, в которую будут скопированы данные после доставки диска в центр обработки данных Azure.
3. Используйте [инструмент импорта и экспорта Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) — служебную программу командной строки. Ниже приведен пример фрагмента кода, демонстрирующий использование инструмента.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Дополнительные фрагменты кода см. в статье [Использование службы импорта и экспорта Azure для передачи данных в хранилище BLOB-объектов](../storage/common/storage-import-export-service.md).
4. Приведенная выше команда создает файл журнала в указанном расположении. С его помощью вы создадите задание импорта из [портала Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>создание задания импорта;
Теперь можно создать задание импорта, воспользовавшись инструкциями в статье [Использование службы импорта и экспорта Azure для передачи данных в хранилище BLOB-объектов](../storage/common/storage-import-export-service.md) в подразделе **Создание задания импорта**. Помимо других сведений укажите для этого задания импорта файл журнала, созданный при подготовке дисков.

## <a name="physically-ship-the-disks"></a>Физическая доставка дисков
Теперь можно выполнить физическую доставку дисков в центр обработки данных Azure. Там данные копируются в большие двоичные объекты службы хранилища Azure, указанные при создании задания импорта. Кроме того, если при создании задания было решено указать сведения об отслеживании позже, теперь можно вернуться к заданию импорта и обновить номер отслеживания.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Копирование данных из больших двоичных объектов службы хранилища Azure в Azure Data Lake Store
После того как задание импорта перейдет в состояние "Завершено", можно проверить, доступны ли данные в указанных больших двоичных объектах службы хранилища Azure. Затем можно переместить данные из больших двоичных объектов в Azure Data Lake Store, используя различные методы. Сведения о всех доступных вариантах передачи данных см. в разделе [Прием данных в Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

В этом разделе указаны определения JSON, с помощью которых можно создать конвейер фабрики данных Azure для копирования данных. Эти определения JSON доступны на [портале Azure](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), а также в [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Связанная служба источника (большой двоичный объект службы хранилища Azure)
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Связанная служба назначения (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Входной набор данных
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Выходной набор данных
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Конвейер (действие копирования)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Дополнительные сведения см. в статье [Перемещение данных в Azure Data Lake Store и обратно с помощью фабрики данных Azure](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Воссоздание файлов данных в Azure Data Lake Store
Мы начали работу с файлом размером 319 ГБ и разделили его на файлы меньшего размера для передачи с помощью службы импорта и экспорта Azure. Теперь после передачи данных в Azure Data Lake Store мы можем воссоздать исходный файл. Для этого вы также можете воспользоваться следующими командлетами Azure PowerShell.

````
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext -SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
````

## <a name="next-steps"></a>Дополнительная информация
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)
