---
title: "Копирование данных из Teradata с помощью фабрики данных Azure | Документация Майкрософт"
description: "Дополнительные сведения о соединителе Teradata службы фабрики данных, которая позволяет вам копировать данные из базы данных Teradata в хранилища данных, которые поддерживаются фабрикой данных в качестве приемника."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 77cf65930a6d622e54395dd563901977c9d76170
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Копирование данных из Teradata с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-onprem-teradata-connector.md)
> * [Версия 2 — предварительная](connector-teradata.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных Teradata. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если вы используете службу фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data from Teradata using Azure Data Factory](v1/data-factory-onprem-teradata-connector.md) (Перемещение данных из Teradata с использованием фабрики данных Azure).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Вы можете копировать данные из базы данных Teradata в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Teradata поддерживает:

- Teradata **версии 12 и выше**;
- копирование данных с использованием **базовой** проверки подлинности или проверки подлинности **Windows**.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот соединитель Teradata, вам нужно:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установить на машине интегрированной среды выполнения [Поставщик данных .NET для Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) версии 14 или более поздней.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы содержат сведения о свойствах, которые используются для определения объектов фабрики данных, относящихся к соединителю Teradata.

## <a name="linked-service-properties"></a>Свойства связанной службы

Связанная служба Teradata поддерживает следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Teradata** | Да |
| server | Имя сервера Teradata. | Да |
| authenticationType | Тип проверки подлинности, используемый для подключения к базе данных Teradata.<br/>Допустимые значения: **Basic** и **Windows**. | Да |
| Имя пользователя | Укажите имя пользователя для подключения к базе данных Teradata. | Да |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле в качестве SecureString. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Да |

**Пример**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных Teradata.

Чтобы скопировать данные из Teradata, задайте для свойства type набора данных значение **RelationalTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **RelationalTable**. | Да |
| tableName | Имя таблицы в базе данных Teradata. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "TeradataDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых Teradata в качестве источника.

### <a name="teradata-as-source"></a>Teradata в качестве источника

Чтобы копировать данные из Teradata, задайте тип источника **RelationalSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **RelationalSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Сопоставление типов для Teradata

При копировании данных из Teradata используются следующие сопоставления типов данных Teradata с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Типы данных Teradata | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BigInt |Int64 |
| BLOB-объект |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Строка |
| Clob |Строка |
| Дата |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Строка |
| Целое число  |Int32 |
| Interval Day |TimeSpan |
| Interval Day To Hour |TimeSpan |
| Interval Day To Minute |TimeSpan |
| Interval Day To Second |TimeSpan |
| Interval Hour |TimeSpan |
| Interval Hour To Minute |TimeSpan |
| Interval Hour To Second |TimeSpan |
| Interval Minute |TimeSpan |
| Interval Minute To Second |TimeSpan |
| Interval Month |Строка |
| Interval Second |TimeSpan |
| Interval Year |Строка |
| Interval Year To Month |Строка |
| Number |Double |
| Period(Date) |Строка |
| Period(Time) |Строка |
| Period(Time With Time Zone) |Строка |
| Period(Timestamp) |Строка |
| Period(Timestamp With Time Zone) |Строка |
| SmallInt |Int16 |
| Время |TimeSpan |
| Time With Time Zone |Строка |
| Timestamp |DateTime |
| Timestamp With Time Zone |Datetimeoffset |
| VarByte |Byte[] |
| VarChar |Строка |
| VarGraphic |Строка |
| Xml |Строка |


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
