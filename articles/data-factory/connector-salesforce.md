---
title: Копирование данных из Salesforce и в Salesforce с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Salesforce в поддерживаемые приемники данных или из поддерживаемых источников данных в Salesforce с помощью действия копирования в конвейере фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: f4de97ef2df5351ac7e8574717ee1439b54a90e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Копирование данных в Salesforce и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-salesforce-connector.md)
> * [Версия 2 — предварительная](connector-salesforce.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в Salesforce или из Salesforce. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется версия 1 фабрики данных, которая является общедоступной, см. статью [Перемещение данных из Salesforce с помощью фабрики данных Azure](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Salesforce можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. В свою очередь, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в Salesforce. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Salesforce поддерживает следующее.

- Выпуски Salesforce Developer, Professional, Enterprise и Unlimited.
- Копирование данных в рабочую среду, песочницу или личный домен Salesforce, а также из них.

## <a name="prerequisites"></a>предварительным требованиям

В Salesforce требуется включить разрешение API. Дополнительные сведения о включении доступа к API в Salesforce с помощью набора разрешений см. [здесь](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/).

## <a name="salesforce-request-limits"></a>Ограничения запросов Salesforce

Для Salesforce установлены ограничения на общее число запросов API и одновременных запросов API. Обратите внимание на следующие моменты.

- Если количество одновременных запросов превышает ограничение, выполняется регулирование и возникают случайные ошибки.
- В случае превышения ограничения на общее число запросов учетная запись Salesforce блокируется на 24 часа.

Кроме того, в обоих случаях вы можете получить сообщение об ошибке "REQUEST_LIMIT_EXCEEDED". Дополнительные сведения см. в разделе API request limits (Ограничения запросов API) руководства об [ограничениях для разработчика Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Salesforce.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Salesforce поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип |Для свойства type нужно задать значение **Salesforce**. |Yes |
| environmentUrl | Укажите URL-адрес экземпляра Salesforce. <br> Значение по умолчанию — `"https://login.salesforce.com"`. <br> Чтобы скопировать данные из песочницы, укажите `"https://test.salesforce.com"`. <br> Чтобы скопировать данные из пользовательского домена, укажите URL-адрес, например `"https://[domain].my.salesforce.com"`. |Нет  |
| Имя пользователя |Укажите имя пользователя для учетной записи пользователя. |Yes |
| password |Укажите пароль для учетной записи пользователя.<br/><br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| securityToken |Укажите маркер безопасности для учетной записи пользователя. Инструкции по получению и сбросу маркера безопасности см. в [этом разделе](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Общие сведения о маркере безопасности см. в статье [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Безопасность и API).<br/><br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!IMPORTANT]
>При копировании данных в Salesforce среду выполнения интеграции Azure по умолчанию нельзя использовать для копирования. Другими словами, если связанная служба источника не имеет указанной среды выполнения интеграции, явным образом [создайте IR Azure](create-azure-integration-runtime.md#create-azure-ir) в расположении вблизи экземпляра Salesforce. Как показано в следующем примере, ассоциируйте связанную службу Salesforce.

**Пример хранения данных в фабрике данных**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример хранения учетных данных в хранилище ключей**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных Salesforce.

Чтобы скопировать данные в Salesforce, а также из Salesforce, для свойства type набора данных установите значение **SalesforceObject**. Поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type нужно задать значение **SalesforceObject**.  | Yes |
| objectApiName | Имя объекта Salesforce, из которого извлекаются данные. | "Нет" для источника, "Да" для приемника |

> [!IMPORTANT]
> Для любых настраиваемых объектов **имя API** должно содержать приставку "__c".

![Фабрика данных — подключение к Salesforce: имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Пример.**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Для обеспечения обратной совместимости: если для копирования данных из Salesforce используется прежний тип набора данных (RelationalTable), он будет работать, пока не появится предложение переключиться на новый тип (SalesforceObject).

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **RelationalTable**. | Yes |
| tableName | Имя таблицы в Salesforce | Нет (если свойство query указано в источнике действия) |

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce в качестве источника данных

Чтобы копировать данные из Salesforce, установите тип источника **SalesforceSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **SalesforceSource**. | Yes |
| query |Используйте пользовательский запрос для чтения данных. Вы можете использовать запрос SQL-92 или запрос, написанный на [объектно-ориентированном языке запросов Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Например, `select * from MyTable__c`. | Нет (если для набора данных задано свойство tableName) |
| readBehavior | Указывает, следует ли запрашивать существующие записи или все записи, включая удаленные. Если значение не задано, по умолчанию используется первое значение. <br>Допустимые значения: **query** (по умолчанию), **queryAll**.  | Нет  |

> [!IMPORTANT]
> Для любых настраиваемых объектов **имя API** должно содержать приставку "__c".

![Фабрика данных — подключение к Salesforce: список имен API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Для обеспечения обратной совместимости: если для копирования данных из Salesforce используется прежний тип источника (RelationalSource), он будет работать, пока не появится предложение переключиться на новый тип (SalesforceSource).

### <a name="salesforce-as-a-sink-type"></a>Salesforce в качестве типа приемника

Чтобы скопировать данные в Salesforce, установите тип приемника **SalesforceSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **SalesforceSink**. | Yes |
| writeBehavior | Поведение операции при записи.<br/>Допустимые значения: **Insert** (Вставка) и **Upsert** (Вставка-обновление). | Нет (по умолчанию используется Insert) |
| externalIdFieldName | Имя поля для внешнего идентификатора при операции upsert. Это поле должно быть определено в объекте Salesforce как External Id Field (Поле внешнего идентификатора). Оно не может иметь значения NULL в соответствующих входных данных. | "Да" для операции Upsert (Вставка-обновление) |
| writeBatchSize | Количество строк данных, записываемых в Salesforce одним пакетом. | Нет (значение по умолчанию — 5,000) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или обновления (update) оставьте данные в целевом объекте без изменений. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или обновления (update) обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

**Пример: приемник Salesforce в действии копирования**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Советы по запросам

### <a name="retrieve-data-from-a-salesforce-report"></a>Получение данных из отчета Salesforce

Вы можете извлекать данные из отчетов Salesforce, указывая запросы в формате `{call "<report name>"}`. Например, `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Получение удаленных записей из корзины Salesforce

Чтобы запросить из корзины Salesforce обратимо удаленные записи, укажите в своем запросе **"IsDeleted = 1"**. Например: 

* Чтобы запросить только удаленные записи, укажите "select \* from MyTable__c **where IsDeleted= 1**".
* Чтобы запросить все записи, включая существующие и удаленные, укажите "select from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**".

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Извлечение данных с использованием предложения where для столбца даты и времени

При указании запроса SOQL или SQL обратите внимание на различие в формате даты и времени. Например: 

* **Пример SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Пример SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Сопоставление типов данных для Salesforce

При копировании данных из Salesforce для промежуточных типов данных фабрики данных используются следующие сопоставления из типов данных Salesforce. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Salesforce | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| Автонумерация |Строка |
| Флажок |Логическое |
| Валюта |Double |
| Дата |Datetime |
| Дата и время |Datetime |
| Email |Строка |
| Идентификатор |Строка |
| Связь для подстановки |Строка |
| Список множественного выбора |Строка |
| Number |Double |
| Процент |Double |
| Номер телефона |Строка |
| Список выбора |Строка |
| текст |Строка |
| Текстовое поле |Строка |
| Текстовое поле (длинное) |Строка |
| Текстовое поле (расширенное) |Строка |
| Текст (зашифрованный) |Строка |
| URL-адрес |Строка |

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.