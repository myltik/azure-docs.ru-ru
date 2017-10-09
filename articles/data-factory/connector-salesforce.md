---
title: "Копирование данных из Salesforce с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из Salesforce на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c819b3e3e715427632e793ce77d31554914d248e
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Копирование данных из Salesforce с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-salesforce-connector.md)
> * [Версия 2 — предварительная](connector-salesforce.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных Salesforce. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из Salesforce с помощью фабрики данных Azure](v1/data-factory-salesforce-connector.md).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Данные из базы данных Salesforce можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Salesforce поддерживает следующие выпуски Salesforce: **Developer Edition, Professional Edition, Enterprise Edition или Unlimited Edition**. Он также поддерживает копирование данных из **рабочей среды Salesforce, песочницы и пользовательского домена**.

## <a name="prerequisites"></a>Предварительные требования

* В Salesforce требуется включить разрешение API. Дополнительные сведения о включении доступа к API в Salesforce с помощью набора разрешений см. [здесь](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/).

## <a name="salesforce-request-limits"></a>Ограничения запросов Salesforce

Для Salesforce установлены ограничения на общее число запросов API и одновременных запросов API. Обратите внимание на следующие моменты.

- Если количество одновременных запросов превышает ограничение, выполняется регулирование и возникают случайные ошибки.
- В случае превышения ограничения на общее число запросов учетная запись Salesforce блокируется на 24 часа.

Кроме того, в обоих случаях вы можете получить ошибку REQUEST_LIMIT_EXCEEDED. Дополнительные сведения см. в разделе "API Request Limits" (Ограничения запросов API) статьи об [ограничениях для разработчика Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Salesforce.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Salesforce поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type |Для свойства type нужно задать значение **Salesforce**. |Да |
| environmentUrl | Укажите URL-адрес экземпляра Salesforce. <br><br> Значение по умолчанию — `"https://login.salesforce.com"`. <br> Чтобы скопировать данные из песочницы, укажите `"https://test.salesforce.com"`. <br> Чтобы скопировать данные из пользовательского домена, укажите URL-адрес, например `"https://[domain].my.salesforce.com"`. |Нет |
| Имя пользователя |Укажите имя пользователя для учетной записи пользователя. |Да |
| password |Укажите пароль для учетной записи пользователя. |Да |
| securityToken |Укажите маркер безопасности для учетной записи пользователя. Инструкции по получению и сбросу маркера безопасности см. в статье [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Получение маркера безопасности). Общие сведения о маркере безопасности см. в статье [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Безопасность и API). |Да |

**Пример**

```json
{
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
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Salesforce.

Чтобы скопировать данные из Salesforce, установите свойство type набора данных **RelationalTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **RelationalTable**. | Да |
| tableName | Имя таблицы в базе данных Salesforce. | Нет (если свойство query указано в источнике действия) |

> [!IMPORTANT]
> Имя API для любых настраиваемых объектов должно содержать приставку __c.

![Фабрика данных — подключение к Salesforce — имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Пример**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Salesforce.

### <a name="salesforce-as-source"></a>Salesforce в качестве источника

Чтобы копировать данные из Salesforce, установите тип источника **RelationalSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **RelationalSource**. | Да |
| query |Используйте пользовательский запрос для чтения данных. Вы можете использовать запрос SQL-92 или запрос, написанный на [объектно-ориентированном языке запросов Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Например, `select * from MyTable__c`. | Нет (если для набора данных задано свойство tableName) |

> [!IMPORTANT]
> Имя API для любых настраиваемых объектов должно содержать приставку __c.

![Фабрика данных — подключение к Salesforce — имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Пример**

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
                "type": "RelationalSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>Советы по запросам

### <a name="retrieving-data-from-salesforce-report"></a>Извлечение данных из отчета Salesforce

Из отчетов Salesforce можно извлекать данные, указывая запросы в формате `{call "<report name>"}. Example: `"{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Восстановление удаленных записей из корзины Salesforce

Чтобы запросить из корзины Salesforce обратимо удаленные записи, укажите в своем запросе **"IsDeleted = 1"**. Например,

* чтобы запросить только удаленные записи, укажите select * from MyTable__c **where IsDeleted= 1**
* Чтобы запросить все записи, включая существующие и удаленные, укажите select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Получение данных с использованием предложения WHERE в столбце даты и времени

При указании запроса SOQL или SQL обратите внимание на различие в формате даты и времени. Например:

* **Пример SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **Пример SQL**: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Сопоставление типов данных для Salesforce

При копировании данных из Salesforce для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных Salesforce. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Salesforce | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| Автонумерация |Строка |
| Флажок |Логический |
| Валюта |Double |
| Дата |DateTime |
| Дата и время |DateTime |
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


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
