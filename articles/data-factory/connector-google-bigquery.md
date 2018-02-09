---
title: "Копирование данных из Google BigQuery с помощью фабрики данных Azure (бета-версия) | Документация Майкрософт"
description: "Узнайте, как копировать данные из Google BigQuery в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Копирование данных из Google BigQuery с помощью фабрики данных Azure (бета-версия)

Из этой статьи вы узнаете, как с помощью действия копирования в фабрике данных Azure копировать данные из Google BigQuery. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной, ознакомьтесь со статьей [Перемещение данных с помощью действия копирования](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Сейчас этот соединитель доступен в бета-версии. Попробуйте поработать с ним и оставьте свой отзыв. Не используйте его в рабочих средах.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Google BigQuery можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

 В фабрике данных предоставляется встроенный драйвер, который обеспечивает подключение. Поэтому не нужно вручную устанавливать драйвер для использования этого соединителя.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Google BigQuery.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google BigQuery поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **GoogleBigQuery**. | Yes |
| project | Идентификатор проекта BigQuery по умолчанию для отправки запросов.  | Yes |
| additionalProjects | Список разделенных запятыми идентификаторов общедоступных проектов BigQuery для доступа.  | Нет  |
| requestGoogleDriveScope | Определяет, следует ли подавать запрос на доступ к Google Drive. Если разрешить доступ к Google Drive, включится поддержка для федеративных таблиц, которые объединяют данные BigQuery с данными с Google Drive. Значение по умолчанию — **false**.  | Нет  |
| authenticationType | Механизм OAuth 2.0 для аутентификации. ServiceAuthentication может использоваться только в локальных средах выполнения интеграции. <br/>Допустимые значения: **ServiceAuthentication** и **UserAuthentication**. | Yes |
| refreshtoken | Маркер обновления, полученный из Google, используемый для авторизации доступа UserAuthentication к BigQuery. Это поле можно пометить как SecureString, чтобы безопасно хранить его в фабрике данных. Вы также можете сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при копировании данных. Подробные сведения см. в руководстве по [хранению учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Нет  |
| email | Идентификатор электронной почты учетной записи службы, используемый для ServiceAuthentication. Может использоваться только в локальной среде выполнения интеграции.  | Нет  |
| keyFilePath | Полный путь к файлу ключа P12, используемый для аутентификации адреса электронной почты учетной записи службы. Может использоваться только в локальной среде выполнения интеграции.  | Нет  |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС, используемые для проверки сервера при подключении по протоколу SSL. Это свойство можно задать, только если SSL используется в локальной среде выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе со средой выполнения интеграции.  | Нет  |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного системного хранилища или из указанного PEM-файла. Значение по умолчанию — **false**.  | Нет  |

**Пример.**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Google BigQuery.

Чтобы скопировать данные из Google BigQuery, установите для свойства type набора данных значение **GoogleBigQueryObject**. В этом типе набора данных нет дополнительных свойств для определенного типа.

**Пример**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых типом источника Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource в качестве типа источника

Чтобы копировать данные Google BigQuery, установите тип источника **GoogleBigQuerySource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type источника действия копирования необходимо задать значение **GoogleBigQuerySource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
