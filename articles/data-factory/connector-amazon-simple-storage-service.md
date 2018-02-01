---
title: "Копирование данных из Amazon Simple Storage Service с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из Amazon Simple Storage Service (S3) в поддерживаемые хранилища данных приемника с помощью фабрики данных Azure."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 6df7d74d572a59c83105905fbe0a9e218aadc28f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Копирование данных из Amazon Simple Storage Service с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Версия 2 — предварительная](connector-amazon-simple-storage-service.md)

В этой статье объясняется, как с помощью действия копирования в фабрике данных Azure копировать данные в хранилище BLOB-объектов Azure и обратно. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из Amazon Simple Storage Service с помощью фабрики данных Azure](v1/data-factory-amazon-simple-storage-service-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из любого поддерживаемого в качестве источника хранилища данных в Azure Data Lake Store или из Azure Data Lake Store в любое поддерживаемое в качестве приемника хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Amazon S3 поддерживает копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы скопировать данные из Amazon S3, убедитесь, что вы предоставили следующие разрешения:

- `s3:GetObject` и `s3:GetObjectVersion` для операций с объектами Amazon S3.
- `s3:ListBucket` для операций в контейнере Amazon S3. Если вы используете мастер копирования фабрики данных, также требуется разрешение `s3:ListAllMyBuckets`.

Полный список разрешений Amazon S3 см. в статье, посвященной [назначению разрешений в политике](http://docs.aws.amazon.com/amazons3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Amazon S3.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Amazon S3 поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства типа необходимо задать значение **AmazonS3**. | Yes |
| accessKeyId | Идентификатор секретного ключа доступа. |Yes |
| secretAccessKey | Сам секретный ключ доступа. Пометьте это поле в качестве SecureString. |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

>[!NOTE]
>Для этого соединителя требуются ключи доступа к учетной записи IAM для копирования данных из Amazon S3. [Временные учетные данные безопасности](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) не поддерживаются.
>

Вот пример: 

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Amazon S3.

Чтобы скопировать данные из Amazon S3, установите свойство типа набора данных **AmazonS3Object**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство типа для набора данных должно быть: **AmazonS3Object**. |Yes |
| bucketName | Имя контейнера S3. |Yes |
| key | Ключ объекта S3. Применяется, только если не указан префикс. |Нет  |
| prefix | Префикс для ключа объекта S3. Выбираются объекты, ключи которых начинаются с этого префикса. Применяется, только если не указан ключ. |Нет  |
| версия | Версия объекта S3, если включено управление версиями S3. |Нет  |
| свойства | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет  |

> [!NOTE]
> **Свойства bucketName и key** указывают расположение объекта S3, где контейнер — это корневой контейнер для объектов S3, а ключ — полный путь к объекту S3.

**Пример. Использование префикса**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Пример. Использование ключа и версии (необязательно)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Data Lake.

### <a name="amazon-s3-as-source"></a>Amazon S3 в качестве источника

Чтобы скопировать данные из Amazon S3, задайте тип источника **FileSystemSource** (включающий Amazon S3) в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **FileSystemSource**. |Yes |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет  |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.