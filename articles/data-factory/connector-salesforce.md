---
title: "Копирование данных в Salesforce или из Salesforce с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из Salesforce в поддерживаемые приемники данных и (или) из поддерживаемых источников данных в Salesforce с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: 7cd86922b0445fc81766ca54080e2fd3e64a6c61
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-fromto-salesforce-using-azure-data-factory"></a>Копирование данных в Salesforce или из Salesforce с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-salesforce-connector.md)
> * [Версия 2 — предварительная](connector-salesforce.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в Salesforce или из Salesforce. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из Salesforce с помощью фабрики данных Azure](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно копировать из Salesforce в любой поддерживаемый приемник данных или из любого поддерживаемого источника данных Salesforce. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Salesforce поддерживает следующее.

- Любой из следующих выпусков Salesforce: **Developer Edition, Professional Edition, Enterprise Edition или Unlimited Edition**.
- Копирование данных в **рабочую среду, песочницу или пользовательский домен Salesforce**, а также из них.

## <a name="prerequisites"></a>Необходимые компоненты

* В Salesforce требуется включить разрешение API. Дополнительные сведения о включении доступа к API в Salesforce с помощью набора разрешений см. [здесь](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/).

## <a name="salesforce-request-limits"></a>Ограничения запросов Salesforce

Для Salesforce установлены ограничения на общее число запросов API и одновременных запросов API. Обратите внимание на следующие моменты.

- Если количество одновременных запросов превышает ограничение, выполняется регулирование и возникают случайные ошибки.
- В случае превышения ограничения на общее число запросов учетная запись Salesforce блокируется на 24 часа.

Кроме того, в обоих случаях вы можете получить ошибку REQUEST_LIMIT_EXCEEDED. Дополнительные сведения см. в разделе "API Request Limits" (Ограничения запросов API) статьи об [ограничениях для разработчика Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Salesforce.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Salesforce поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательное значение |
|:--- |:--- |:--- |
| Тип |Для свойства type нужно задать значение **Salesforce**. |Yes |
| environmentUrl | Укажите URL-адрес экземпляра Salesforce. <br> Значение по умолчанию — `"https://login.salesforce.com"`. <br> Чтобы скопировать данные из песочницы, укажите `"https://test.salesforce.com"`. <br> Чтобы скопировать данные из пользовательского домена, укажите URL-адрес, например `"https://[domain].my.salesforce.com"`. |Нет  |
| Имя пользователя |Укажите имя пользователя для учетной записи пользователя. |Yes |
| password |Укажите пароль для учетной записи пользователя.<br/><br/>Вы можете обозначить это поле как SecureString, чтобы хранить его в ADF с высоким уровнем безопасности, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). |Yes |
| securityToken |Укажите маркер безопасности для учетной записи пользователя. Инструкции по получению и сбросу маркера безопасности см. в статье [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Получение маркера безопасности). Общие сведения о маркере безопасности см. в статье [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Безопасность и API).<br/><br/>Вы можете обозначить это поле как SecureString, чтобы хранить его в ADF с высоким уровнем безопасности, или сохранить маркер безопасности в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Не требуется для источника и требуется для приемника, если в связанной с источником службе не используется среда IR |

>[!IMPORTANT]
>При копировании данных **в** Salesforce среду Integration Runtime Azure по умолчанию нельзя использовать для копирования. Другими словами, если для связанной с источником службы не указана среда выполнения интеграции, явным образом [создайте среду Azure IR](create-azure-integration-runtime.md#create-azure-ir), расположенную вблизи вашей системы Salesforce, и сопоставьте ее в связанной службе Salesforce, как показано в следующем примере.

**Пример: хранение учетных данных в ADF**

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

**Пример: хранение учетных данных в Azure Key Vault**

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

Чтобы скопировать данные в Salesforce или из Salesforce, для свойства type набора данных установите значение **SalesforceObject**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательное значение |
|:--- |:--- |:--- |
| Тип | Для свойства type нужно задать значение **SalesforceObject**.  | Yes |
| objectApiName | Имя объекта Salesforce, из которого извлекаются данные. | "Нет" для источника, "Да" для приемника |

> [!IMPORTANT]
> Имя API для любых настраиваемых объектов должно содержать приставку __c.

![Фабрика данных — подключение к Salesforce — имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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
>Для сохранения обратной совместимости допускается копирование данных из Salesforce с использованием прежнего типа набора данных (RelationalTable). Но мы рекомендуем вам изменить тип на новое значение (SalesforceObject).

| Свойство | ОПИСАНИЕ | Обязательное значение |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **RelationalTable**. | Yes |
| tableName | Имя таблицы в Salesforce | Нет (если свойство query указано в источнике действия) |

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Salesforce.

### <a name="salesforce-as-source"></a>Salesforce в качестве источника

Чтобы копировать данные из Salesforce, установите тип источника **SalesforceSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательное значение |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **SalesforceSource**. | Yes |
| query |Используйте пользовательский запрос для чтения данных. Вы можете использовать запрос SQL-92 или запрос, написанный на [объектно-ориентированном языке запросов Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Например, `select * from MyTable__c`. | Нет (если для набора данных задано свойство tableName) |

> [!IMPORTANT]
> Имя API для любых настраиваемых объектов должно содержать приставку __c.

![Фабрика данных — подключение к Salesforce — имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Для сохранения обратной совместимости допускается копирование данных из Salesforce с использованием прежнего типа набора данных (RelationalSource). Но мы рекомендуем вам изменить тип на новое значение (SalesforceSource).

### <a name="salesforce-as-sink"></a>Salesforce в качестве приемника

Чтобы скопировать данные в Salesforce, установите тип приемника **SalesforceSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательное значение |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **SalesforceSink**. | Yes |
| writeBehavior | Поведение операции при записи.<br/>Допустимые значения: **Insert** (Вставка) и **Upsert** (Вставка-обновление). | Нет (по умолчанию используется Insert) |
| externalIdFieldName | Имя поля для внешнего идентификатора при операции upsert. Это поле должно быть определено в объекте Salesforce как External Id Field (Поле внешнего идентификатора), и в соответствующих входных данных не должно быть значений NULL. | "Да" для операции Upsert (Вставка-обновление) |
| writeBatchSize | Количество строк данных, записываемых в Salesforce одним пакетом. | Нет (по умолчанию используется значение 5000) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных во время операции записи.<br/>Допустимые значения: **true** (да) и **false** (нет).<br>- **true** обозначает, что данные в целевом объекте остаются без изменений при операциях вставки-обновления (upsert) или обновления (update), а при операции вставки (insert) записывается установленное значение по умолчанию.<br/>- **false** обозначает, что данные в целевом объекте заменяются значением NULL при выполнении операции вставки-обновления (upsert) или обновления (update), а выполнении операции вставки (insert) записывается значение NULL. | Нет (по умолчанию используется значение false) |

### <a name="example-salesforce-sink-in-copy-activity"></a>Пример: приемник Salesforce в действии копирования

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

### <a name="retrieving-data-from-salesforce-report"></a>Извлечение данных из отчета Salesforce

Вы можете извлекать данные из отчетов Salesforce, указывая запросы в формате `{call "<report name>"}`. Пример: `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Восстановление удаленных записей из корзины Salesforce

Чтобы запросить из корзины Salesforce обратимо удаленные записи, укажите в своем запросе **"IsDeleted = 1"**. Например,

* чтобы запросить только удаленные записи, укажите select * from MyTable__c **where IsDeleted= 1**
* Чтобы запросить все записи, включая существующие и удаленные, укажите select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Получение данных с использованием предложения WHERE в столбце даты и времени

При указании запроса SOQL или SQL обратите внимание на различие в формате даты и времени. Например: 

* **Пример SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Пример SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Сопоставление типов данных для Salesforce

При копировании данных из Salesforce для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных Salesforce. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

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
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.