---
title: "Копирование данных в хранилище Azure Data Lake Store и из него с помощью фабрики данных | Документация Майкрософт"
description: "Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в Azure Data Lake Store или из Data Lake Store в поддерживаемые приемники хранилищ с помощью фабрики данных."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e4f92c0c4d9307837ef6c760acd8eaa846938ded
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>Копирование данных в хранилище Azure Data Lake Store и из него с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-azure-datalake-connector.md)
> * [Версия 2 — предварительная](connector-azure-data-lake-store.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из хранилища Azure Data Lake Store и в него. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Copy data to and from Data Lake Store by using Data Factory](v1/data-factory-azure-datalake-connector.md) (Копирование данных в хранилище Data Lake Store или из него с помощью фабрики данных Azure).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Данные можно скопировать из любого поддерживаемого в качестве источника хранилища данных в Azure Data Lake Store или из Azure Data Lake Store в любое поддерживаемое в качестве приемника хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Azure Data Lake Store поддерживает:

- Копирование службы с использованием проверки подлинности на основе **субъекта-службы**.
- Копирование файлов "как есть", анализ или создание файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Начало работы
Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md). 

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Azure Data Lake Store.

## <a name="linked-service-properties"></a>Свойства связанной службы

Вы можете создать связанную службу Azure Data Lake Store с использованием проверки подлинности субъекта-службы.

При использовании проверки подлинности на основе субъекта-службы необходимо зарегистрировать сущность приложения в Azure Active Directory (Azure AD) и предоставить ей доступ к Data Lake Store. Подробные инструкции см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Запишите следующие значения, которые используются для определения связанной службы:

- Идентификатор приложения
- Ключ приложения
- Tenant ID

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type следует задать значение **AzureDataLakeStore** | Да |
| dataLakeStoreUri | Сведения об учетной записи Azure Data Lake Store. Эти данные принимают один из следующих форматов: `https://[accountname].azuredatalakestore.net/webhdfs/v1` или `adl://[accountname].azuredatalakestore.net/`. | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле в качестве SecureString. | Да |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Да |
| subscriptionId | Идентификатор подписки Azure, к которой принадлежит учетная запись Data Lake Store. | Необходимо для приемника |
| имя_группы_ресурсов | Имя группы ресурсов Azure, к которой принадлежит учетная запись Data Lake Store. | Необходимо для приемника |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

**Пример**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Azure Data Lake Store.

Чтобы скопировать данные в Azure Data Lake Store или из него, укажите для свойства type набора данных значение **AzureDataLakeStoreFile**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **AzureDataLakeStoreFile**. |Да |
| folderPath | Путь контейнеру и папке в хранилище файлов. Пример: rootfolder/subfolder/ |Да |
| fileName | Укажите имя файла в **folderPath**, если нужно скопировать в него или из него. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке.<br/><br/>Если fileName для выходного наборах данных не указан, а **preserveHierarchy** не указан в действии приемника, действие копирования автоматически создаст имя файла в следующем формате: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Например, `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`. |Нет |
| свойства | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет |

**Пример**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "myfile.csv.gz",
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

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store в качестве источника

Чтобы скопировать данные из Azure Data Lake Store, задайте тип источника **AzureDataLakeStoreSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **AzureDataLakeStoreSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store в качестве приемника

Чтобы копировать данные в большой двоичный объект Azure, установите тип приемника **AzureDataLakeStoreSink** в действии копирования. Следующие свойства поддерживаются в разделе **sink**:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type приемника действия копирования должно иметь значение **AzureDataLakeStoreSink**. |Да |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из хранилища данных на основе файлов.<br/><br/>Допустимые значения:<br/><b>- PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>- FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированное имя. <br/><b>- MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано. | Нет |

**Пример**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="recursive-and-copybehavior-examples"></a>Примеры recursive и copyBehavior

В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

| recursive | copyBehavior | Структура папок источника | Результаты цели |
|:--- |:--- |:--- |:--- |
| Да |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается с такой же структурой, как и исходная папка.<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл5" |
| Да |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| нет |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. Автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
