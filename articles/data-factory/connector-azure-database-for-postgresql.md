---
title: Копирование данных из базы данных Azure для PostgreSQL с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из базы данных Azure для PostgreSQL в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: b47dbf081d857d0c6eb5e1bd4eb9781c4c894698
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615943"
---
# <a name="copy-data-from-azure-database-for-postgresql-using-azure-data-factory"></a>Копирование данных из базы данных Azure для PostgreSQL с помощью фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных Azure для PostgreSQL. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из базы данных Azure для PostgreSQL можно скопировать в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю базы данных Azure для PostgreSQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных Azure для PostgreSQL поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzurePostgreSql**. | Yes |
| connectionString | Строка подключения к базе данных Azure для PostgreSQL через интерфейс ODBC. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

Типичная строка подключения — `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>@admstest;Password=<Password>`. Дополнительные свойства, которые вы можете установить в вашем случае:

| Свойство | ОПИСАНИЕ | Параметры | Обязательно |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Метод, используемый драйвером для шифрования данных, отправленных между драйвером и сервером базы данных. (например, `ValidateServerCertificate=<0/1/6>;`| 0 (без шифрования) **(по умолчанию)** -1 (SSL) или 6 (RequestSSL) | Нет  |
| ValidateServerCertificate (VSC) | Определяет, проверяет ли драйвер сертификат, отправленный сервером базы данных, когда включено шифрование SSL (метод шифрования = 1). (например, `ValidateServerCertificate=<0/1>;`| 0 (отключено) **(по умолчанию)** -1 (включено) | Нет  |

**Пример.**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>@admstest;Password=<Password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных базы данных Azure для PostgreSQL.

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для свойства type набора данных значение **AzurePostgreSqlTable**. В этом типе набора данных нет дополнительных свойств для определенного типа.

**Пример**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником базы данных Azure для PostgreSQL.

### <a name="azurepostgresqlsource-as-source"></a>AzurePostgreSqlSource в качестве источника

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для типа источника в действии копирования значение **AzurePostgreSqlSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **AzurePostgreSqlSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
