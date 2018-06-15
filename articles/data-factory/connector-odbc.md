---
title: Копирование данных из источников ODBC с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из источников OData на поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: f1ab981247191db0abebd67701b07bf48b934292
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617116"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Копирование данных из хранилищ данных ODBC и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-odbc-connector.md)
> * [Версия 2 — предварительная](connector-odbc.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в хранилище данных ODBC и из него. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из источника OData с помощью фабрики данных Azure](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно копировать из источника ODBC в любой поддерживаемый приемник данных или из любого поддерживаемого приемника ODBC. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель ODBC поддерживает копирование данных из **любого хранилища данных, совместимого с ODBC**, и в него с использованием **базовой** или **анонимной** проверки подлинности.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы использовать этот соединитель ODBC, сделайте следующее:

- Настройте локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите на компьютере среды выполнения интеграции драйвер ODBC для хранилища данных.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю ODBC.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы ODBC поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Odbc** | Yes |
| connectionString | Строка подключения, исключающая учетные данные. Можно указать строку подключения вида `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` или использовать системное имя источника данных (DSN), которое вы настроили на компьютере Integration Runtime с помощью `"DSN=<name of the DSN on IR machine>;"` (вы все равно должны указать соответствующие учетные данные в связанной службе).<br>Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md).| Yes |
| authenticationType | Тип проверки подлинности, используемый для подключения к хранилищу данных ODBC.<br/>Допустимые значения: **Basic**, **Anonymous**. | Yes |
| userName | При использовании обычной проверки подлинности укажите имя пользователя. | Нет  |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет  |
| credential | Учетные данные в строке подключения, используемые для получения доступа и указанные в формате "драйвер-определенное свойство-значение". Пример: `"RefreshToken=<secret refresh token>;"`. Пометьте это поле в качестве SecureString. | Нет  |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Yes |

**Пример 1. Использование обычной проверки подлинности**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**Пример 2. Использование анонимной проверки подлинности**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных ODBC.

Чтобы скопировать данные из хранилища данных, совместимого с ODBC, и в него, установите свойство type набора данных **RelationalTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **RelationalTable**. | Yes |
| tableName | Имя таблицы в хранилище данных ODBC. | Нет для источника (если свойство query указано в источнике действия).<br/>Да для приемника. |

**Пример**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником ODBC.

### <a name="odbc-as-source"></a>ODBC в качестве источника

Чтобы скопировать данные из хранилища данных, совместимого с ODBC, установите тип источника **RelationalSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **RelationalSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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

### <a name="odbc-as-sink"></a>ODBC в качестве приемника

Чтобы скопировать данные в хранилище данных, совместимое с ODBC, установите тип приемника в действии копирования **OdbcSink**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **OdbcSink**. | Yes |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br/>Допустимые значения: промежуток времени. Пример: 00:30:00 (30 минут). |Нет  |
| writeBatchSize |Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize.<br/>Допустимые значения: целое число (количество строк). |Нет (по умолчанию — 0 (автоматическое обнаружение)) |
| preCopyScript |Перед записью данных в хранилище данных при каждом запуске указывайте SQL-запрос для выполнения операции копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет  |

> [!NOTE]
> Для параметра writeBatchSize, если он не установлен (автоматически обнаружен), операция копирования сначала определяет, поддерживает ли драйвер пакетные операции, и устанавливает значение 10 000, если это так, или 1, если нет. Если явно задать значение, отличное от 0, действие копирования учитывает значение и во время выполнения заканчивается сбоем, если драйвер не поддерживает пакетные операции.

**Пример.**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>Источник IBM Informix

Вы можете копировать данные из базы данных IBM Informix, используя универсальный соединитель ODBC.

Настройте локальную среду IR на компьютере с доступом к хранилищу данных. Integration Runtime использует драйвер ODBC для Informix, чтобы подключаться к хранилищу данных. Поэтому необходимо установить драйвер на том же компьютере. Например, можно использовать драйвер IBM INFORMIX ODBC DRIVER (64-bit). Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites).

Прежде чем использовать источник Informix в решении фабрики данных, убедитесь, что Integration Runtime может подключаться к хранилищу данных, выполнив инструкции из раздела [Устранение проблем подключения](#troubleshoot-connectivity-issues).

Создайте связанную службу ODBC для связи хранилища данных IBM Informix с фабрикой данных Azure, как показано в следующем примере.

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

В начале статьи приводится подробный обзор использования хранилищ данных ODBC в качестве исходных и целевых хранилищ данных (источников и приемников) в ходе операции копирования.

## <a name="microsoft-access-source"></a>Источник Microsoft Access

Вы можете копировать данные из базы данных Microsoft Access, используя универсальный соединитель ODBC.

Настройте локальную среду IR на компьютере с доступом к хранилищу данных. Среда выполнения интеграции использует драйвер ODBC для Microsoft Access, чтобы подключаться к хранилищу данных. Поэтому необходимо установить драйвер на том же компьютере. Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites).

Прежде чем использовать источник Microsoft Access в решении фабрики данных, убедитесь, что Integration Runtime может подключаться к хранилищу данных, выполнив инструкции из раздела [Устранение проблем подключения](#troubleshoot-connectivity-issues).

Создайте связанную службу ODBC для связи базы данных Microsoft Access с фабрикой данных Azure, как показано в следующем примере:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

В начале статьи приводится подробный обзор использования хранилищ данных ODBC в качестве исходных и целевых хранилищ данных (источников и приемников) в ходе операции копирования.

## <a name="ge-historian-source"></a>Источник GE Historian

Вы можете копировать данные из GE Historian, используя универсальный соединитель ODBC.

Настройте локальную среду IR на компьютере с доступом к хранилищу данных. Среда выполнения интеграции использует драйвер ODBC для GE Historian, чтобы подключаться к хранилищу данных. Поэтому необходимо установить драйвер на том же компьютере. Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites).

Прежде чем использовать источник GE Historian в решении фабрики данных, убедитесь, что Integration Runtime может подключаться к хранилищу данных, выполнив инструкции из раздела [Устранение проблем подключения](#troubleshoot-connectivity-issues).

Создайте связанную службу ODBC для связи базы данных Microsoft Access с фабрикой данных Azure, как показано в следующем примере:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
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

В начале статьи приводится подробный обзор использования хранилищ данных ODBC в качестве исходных и целевых хранилищ данных (источников и приемников) в ходе операции копирования.

## <a name="sap-hana-sink"></a>Приемник SAP HANA

>[!NOTE]
>Чтобы скопировать данные из хранилища данных SAP HANA, см. статью [Копирование данных из SAP HANA с помощью фабрики данных Azure](connector-sap-hana.md). Для копирования данных в SAP HANA следуйте этой инструкции по использованию соединителя ODBC. Обратите внимание, что связанные службы для соединителя SAP HANA и соединитель ODBC нельзя использовать повторно, так как они имеют разные типы.
>

Вы можете копировать данные в базу данных SAP HANA, используя универсальный соединитель ODBC.

Настройте локальную среду IR на компьютере с доступом к хранилищу данных. Среда выполнения интеграции использует драйвер ODBC для SAP HANA, чтобы подключаться к хранилищу данных. Поэтому необходимо установить драйвер на том же компьютере. Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites).

Прежде чем использовать приемник SAP HANA в решении фабрики данных, убедитесь, что Integration Runtime может подключаться к хранилищу данных, выполнив инструкции из раздела [Устранение проблем подключения](#troubleshoot-connectivity-issues).

Создайте связанную службу ODBC для связи хранилища данных SAP HANA с фабрикой данных Azure, как показано в следующем примере.

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

В начале статьи приводится подробный обзор использования хранилищ данных ODBC в качестве исходных и целевых хранилищ данных (источников и приемников) в ходе операции копирования.

## <a name="troubleshoot-connectivity-issues"></a>Устранение проблем подключения

Для устранения проблем подключения используйте вкладку **Диагностика** **диспетчера конфигурации для среды выполнения интеграции**.

1. Запустите **диспетчер конфигурации для среды выполнения интеграции**.
2. Перейдите на вкладку **Диагностика** .
3. В разделе "Проверить подключение" выберите **тип** хранилища данных (связанную службу).
4. Укажите **строку подключения** для подключения к хранилищу данных, выберите **проверку подлинности** и введите **имя пользователя**, **пароль** и/или **учетные данные**.
5. Щелкните **Проверить подключение** , чтобы проверить подключение к хранилищу данных.

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
