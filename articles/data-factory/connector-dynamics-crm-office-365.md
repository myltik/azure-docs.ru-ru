---
title: Копирование данных из Dynamics CRM или Dynamics 365 (Common Data Service) и в эти решения с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Microsoft Dynamics CRM или Microsoft Dynamics 365 (Common Data Service) в поддерживаемые хранилища данных, используемые в качестве приемника, или из поддерживаемых исходных хранилищ данных в Dynamics CRM и Dynamics 365 с помощью действия копирования в конвейере фабрики данных.
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
ms.date: 03/16/2018
ms.author: jingwang
ms.openlocfilehash: ea69fdab9ec510f6060b280db3afffb7533a4bda
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Копирование данных из Dynamics 365 (Common Data Service) или Dynamics CRM и в эти решения с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Microsoft Dynamics 365 или Microsoft Dynamics CRM и обратно. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется фабрика данных версии 1, которая является общедоступной, ознакомьтесь со статьей [Перемещение данных с помощью действия копирования](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Dynamics 365 (Common Data Service) или Dynamics CRM можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. В свою очередь, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в Dynamics 365 (Common Data Service) или Dynamics CRM. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Dynamics поддерживает следующие версии Dynamics и типы проверки подлинности. (IFD — сокращение от термина "развертывание с выходом в Интернет".)

| Версии Dynamics | Типы проверки подлинности | Примеры связанной службы |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Оffice 365 | [Проверка подлинности Dynamics Online и Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 (локальная версия) с IFD <br> Dynamics CRM 2016 (локальная версия) с IFD <br> Dynamics CRM 2015 (локальная версия) с IFD | IFD | [Dynamics (локальная версия) с IFD и проверка подлинности IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

В частности, для Dynamics 365 поддерживаются следующие типы приложений:

- Dynamics 365 for Sales;
- Dynamics 365 for Customer Service;
- Dynamics 365 for Field Service;
- Dynamics 365 for Project Service Automation;
- Dynamics 365 for Marketing.

Другие типы приложений (например, Dynamics 365 for Finance and Operations и Dynamics 365 for Talent и т. д.) не поддерживаются.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Dynamics**. | Yes |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | Yes |
| оrganizationName | Имя организации экземпляра Dynamics. | Нет, следует указывать при наличии нескольких экземпляров Dynamics, связанных с пользователем. |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Office365** для Dynamics Online. | Yes |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Yes |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!IMPORTANT]
>При копировании данных в Dynamics среду выполнения интеграции Azure по умолчанию нельзя использовать для копирования. Другими словами, если связанная служба источника не имеет указанной среды выполнения интеграции, явным образом создайте [IR Azure](create-azure-integration-runtime.md#create-azure-ir) в расположении вблизи экземпляра Dynamics. Как показано в следующем примере, ассоциируйте ее в связанной службе Dynamics.

**Пример. Dynamics Online с использованием проверки подлинности Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 и Dynamics CRM (локальная версия) с IFD

*hostName и port являются дополнительными свойствами по сравнению с Dynamics Online.*

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Dynamics**. | Yes |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD необходимо задать значение **OnPremisesWithIfd**.| Yes |
| hostName | Имя узла локального сервера Dynamics. | Yes |
| порт | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| оrganizationName | Имя организации экземпляра Dynamics. | Yes |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | Yes |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Yes |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника |

>[!IMPORTANT]
>Чтобы скопировать данные в Dynamics, явно [создайте среду выполнения интеграции Azure](create-azure-integration-runtime.md#create-azure-ir) в расположении рядом с экземпляром Dynamics. Как показано в следующем примере, ассоциируйте ее в связанной службе.

**Пример. Dynamics (локальная версия) с IFD с использованием проверки подлинности**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Dynamics.

Чтобы копировать данные из Dynamics и обратно, задайте для свойства type набора данных значение **DynamicsEntity**. Поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **DynamicsEntity**. |Yes |
| entityName | Логическое имя сущности, которое требуется получить. | "Нет" для источника (если свойство query указано в источнике действия), "Да" для приемника |

> [!IMPORTANT]
>- При копировании данных из Dynamics раздел structure является обязательным в наборе данных Dynamics. Он определяет столбец имени и тип данных для данных Dynamics, которые требуется скопировать. Дополнительные сведения см. в разделах [Структура набора данных](concepts-datasets-linked-services.md#dataset-structure) и [Сопоставление типов данных для Dynamics](#data-type-mapping-for-dynamics).
>- При копировании данных в Dynamics раздел structure является необязательным в наборе данных Dynamics. В какие столбцы выполняется копирование, определяет схема исходных данных. Если источником является CSV-файл без заголовка, во входном наборе данных укажите параметр structure с именем столбца и тип данных. Они последовательно сопоставляются с полями в CSV-файле.

**Пример.**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых типами источника и приемника Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics в качестве источника данных

Чтобы копировать данные из Dynamics, установите тип источника **DynamicsSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **DynamicsSource**. | Yes |
| query | FetchXML — это защищаемый язык запросов, используемый в Dynamics (Online и локальная версия). См. указанный ниже пример. Дополнительные сведения см. в разделе о [создании запросов на языке FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | "Нет" (если для набора данных задано свойство entityName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Образец запроса FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics в качестве типа приемника

Чтобы копировать данные в Dynamics, установите тип приемника **DynamicsSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **DynamicsSink**. | Yes |
| writeBehavior | Поведение операции при записи.<br/>Допустимое значение: **Upsert**. | Yes |
| writeBatchSize | Количество строк данных, записываемых в Dynamics в каждом пакете. | Нет (значение по умолчанию — 10) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных (за исключением ключевых полей) во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или update оставьте данные в целевом объекте неизменными. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или update обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

>[!NOTE]
>В качестве значения по умолчанию свойства writeBatchSize приемника и свойства [parallelCopies](copy-activity-performance.md#parallel-copy) действия копирования для приемника Dynamics задано 10. Таким образом в Dynamics одновременно отправляются 100 записей.

**Пример.**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Сопоставление типов данных для Dynamics

При копировании данных из Dynamics для промежуточных типов данных фабрики данных используются следующие сопоставления из типов данных Dynamics. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

Настройте соответствующие типы данных для фабрики данных в структуре набора данных на основе вашего типа данных источника Dynamics, используя следующую таблицу сопоставления.

| Тип данных Dynamics | Тип промежуточных данных фабрики данных | Поддерживается в качестве источника | Поддерживается в качестве приемника |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | длинное целое | ✓ | ✓ |
| AttributeTypeCode.Boolean | Логическое | ✓ | ✓ |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Строка | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | |
| AttributeType.ManagedProperty | Логическое | ✓ | |
| AttributeType.Memo | Строка | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Строка | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Типы данных Dynamics AttributeType.CalendarRules и AttributeType.PartyList не поддерживаются.

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
