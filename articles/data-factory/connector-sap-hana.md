---
title: "Копирование данных из SAP HANA с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из SAP HANA на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: 2e66edf63b4bdac1aacfedb64ca1edf207e46a84
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Копирование данных из SAP HANA с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-sap-hana-connector.md)
> * [Версия 2 — предварительная](connector-sap-hana.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных SAP HANA. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из SAP HANA с помощью фабрики данных Azure](v1/data-factory-sap-hana-connector.md).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Данные из базы данных SAP HANA можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP HANA поддерживает:

- Копирование данных из всех версий базы данных SAP HANA.
- Копирование данных из **информационных моделей HANA** (например, представлений Analytic и Calculation) и **таблиц со строками и столбцами** с помощью SQL-запросов.
- копирование данных с использованием **базовой** проверки подлинности или проверки подлинности **Windows**.

> [!NOTE]
> Чтобы скопировать данные **в** хранилище данных SAP HANA, используйте универсальный соединитель ODBC. Подробные сведения см. в разделе о [приемнике SAP HANA](connector-odbc.md#sap-hana-sink). Обратите внимание, что связанные службы для соединителя SAP HANA и соединитель ODBC нельзя использовать повторно, так как они имеют разные типы.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот соединитель SAP HANA, сделайте следующее:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите на компьютере среды выполнения интеграции драйвер ODBC SAP HANA. Драйвер ODBC для SAP HANA можно скачать на странице [SAP Software Download Center](https://support.sap.com/swdc) (Центр загрузки программного обеспечения SAP). Выполните поиск по ключевой фразе **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP HANA.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP HANA поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **SapHana** | Да |
| server | Имя сервера, на котором размещен экземпляр SAP HANA. Если ваш сервер использует настроенный порт, укажите `server:port`. | Да |
| authenticationType | Тип проверки подлинности, используемый для подключения к базе данных SAP HANA.<br/>Допустимые значения: **Базовый**, **Windows**. | Да |
| userName | Имя пользователя, имеющего доступ к серверу SAP. | Да |
| пароль | Пароль для пользователя Пометьте это поле в качестве SecureString. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Да |

**Пример**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных SAP HANA.

Чтобы скопировать данные из SAP HANA, установите свойство type набора данных **RelationalTable**. Сейчас для набора данных SAP HANA типа RelationalTable не поддерживаются какие-либо свойства типа.

**Пример**

```json
{
    "name": "SAPHANADataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA в качестве источника

Чтобы копировать данные из SAP HANA, установите тип источника в действии копирования **RelationalSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **RelationalSource**. | Да |
| query | Указывает SQL-запрос для чтения данных из экземпляра SAP HANA. | Да |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Сопоставление типов данных для SAP HANA

При копировании данных из SAP HANA используются следующие сопоставления типов данных SAP HANA с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных SAP HANA | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| ALPHANUM | Строка |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | DECIMAL |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | Строка |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | Интервал времени |
| TIMESTAMP | DateTime |
| TINYINT | Byte |
| VARCHAR | Строка |

## <a name="known-limitations"></a>Известные ограничения

Существует несколько известных ограничений, действующих при копировании данных из SAP HANA.

- Строки NVARCHAR усекаются до 4000 знаков Юникода.
- SMALLDECIMAL не поддерживается.
- VARBINARY не поддерживается.
- Допустимый диапазон дат: 30.12.1899–31.12.9999.


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
