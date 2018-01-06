---
title: "Копирование данных из Dynamics CRM и Dynamics 365 и обратно с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из Dynamics CRM и Dynamics 365 в поддерживаемые хранилища данных, используемые в качестве приемника, или из поддерживаемых исходных хранилищ данных в Dynamics CRM и Dynamics 365 с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: b9b7091a8cb1de3eefcce77cbf82eedfcb33c787
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Копирование данных из Dynamics CRM или Dynamics 365 и обратно с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Dynamics 365 или Dynamics CRM и обратно. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из Dynamics 365 или Dynamics CRM в любое поддерживаемое хранилище данных, используемое в качестве приемника, а также из любого поддерживаемого исходного хранилища данных в Dynamics 365 или Dynamics CRM. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Dynamics поддерживает следующие версии Dynamics и типы аутентификации (*IFD — это сокращение от Internet Facing Deployment (развертывание с выходом в Интернет)*):

| Версии Dynamics | Типы проверки подлинности | Примеры связанной службы |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Оffice 365 | [Проверка подлинности Dynamics Online и Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 (локальная версия) с IFD <br> Dynamics CRM 2016 (локальная версия) с IFD <br> Dynamics CRM 2015 (локальная версия) с IFD | IFD | [Dynamics (локальная версия) с IFD и проверка подлинности IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

В частности, для Dynamics 365 поддерживаются следующие типы приложений:

- Dynamics 365 for Sales;
- Dynamics 365 for Customer Service;
- Dynamics 365 for Field Service;
- Dynamics 365 for Project Service Automation;
- Dynamics 365 for Marketing.

> [!NOTE]
> Чтобы использовать соединитель Dynamics, сохранять пароль пользователя в хранилище ключей Azure и позвольте запросу устраняйте копирования оттуда, при выполнении копирования данных. Дополнительные сведения о копировании см. в разделе [Свойства связанной службы](#linked-service-properties).

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Dynamics**. | Yes |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | Yes |
| оrganizationName | Имя организации экземпляра Dynamics. | Нет, следует указывать при наличии нескольких экземпляров Dynamics, связанных с пользователем. |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Office365** для Dynamics Online. | Yes |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Yes |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Необходимо поместить пароль в Azure Key Vault и настроить его как AzureKeyVaultSecret. Подробнее о [хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Не для источника, Да для приемника Если связан источник служба не имеет IR |

>[!IMPORTANT]
>При копировании данных **в** Dynamics, по умолчанию среда выполнения интеграции Azure не может использоваться для выполнения копирования. В другие слова, если связанный источник служба не имеет указанного IR, явно [создать IR Azure](create-azure-integration-runtime.md#create-azure-ir) с расположением рядом Dynamics и сопоставления в связанной службы Dynamics в следующем примере.

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
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 и Dynamics CRM (локальная версия) с IFD

*hostName и port являются дополнительными свойствами по сравнению с Dyanmics Online.*

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Dynamics**. | Yes |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD должно иметь значение **OnPremisesWithIfd**.| Yes |
| **Имя узла** | Имя узла локального сервера Dynamics. | Yes |
| **port** | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| оrganizationName | Имя организации экземпляра Dynamics. | Yes |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | Yes |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Yes |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Обратите внимание, что необходимо поместить пароль в Azure Key Vault и настроить его как AzureKeyVaultSecret. Подробнее о [хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника |

>[!IMPORTANT]
>Чтобы копировать данные в Dynamics, явным образом [создайте среду Azure IR](create-azure-integration-runtime.md#create-azure-ir), расположенную вблизи вашей системы Dynamics, и сопоставьте ее в связанной службе, как показано в следующем примере.

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
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Dynamics.

Чтобы копировать данные из Dynamics или обратно, задайте для свойства типа набора данных значение **DynamicsEntity**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **DynamicsEntity**. |Yes |
| entityName | Логическое имя сущности, которое требуется получить. | "Нет" для источника (если свойство query указано в источнике действия), "Да" для приемника |

> [!IMPORTANT]
>- **При копировании данных из Dynamics раздел structure является обязательным** в наборе данных Dynamics, который определяет имя столбца и тип данных для данных Dynamics, которые требуется скопировать. Узнайте больше о [структуре набора данных](concepts-datasets-linked-services.md#dataset-structure) и [сопоставлении типов данных для Dynamics](#data-type-mapping-for-dynamics).
>- **При копировании данных в Dynamics раздел structure является необязательным** в наборе данных Dynamics. В какие столбцы выполняется копирование, определяет схема исходных данных. Если источником является CSV-файл без заголовка, во входном наборе данных укажите параметр structure с именем столбца и тип данных, который последовательно сопоставляется с полями в CSV-файле.

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
        "typePoperties": {
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Dynamics.

### <a name="dynamics-as-source"></a>Dynamics в качестве источника

Чтобы копировать данные из Dynamics, установите тип источника **DynamicsSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **DynamicsSource**  | Yes |
| query  | FetchXML — это защищаемый язык запросов, используемый в Microsoft Dynamics (Online, локальная версия). Узнайте больше о [создании запросов с помощью FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx), ознакомившись со следующим примером. | Нет (если для набора данных задано свойство entityName)  |

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

### <a name="dynamics-as-sink"></a>Dynamics в качестве приемника

Чтобы копировать данные в Dynamics, установите тип приемника **DynamicsSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **DynamicsSink**  | Yes |
| writeBehavior | Поведение операции при записи.<br/>Допустимое значение: **Upsert**. | Yes |
| writeBatchSize | Количество строк данных, записываемых в Dynamics в каждом пакете. | Нет (значение по умолчанию — 10) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных (за исключением ключевых полей) во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>– true: оставить данные в целевом объекте без изменений при выполнении операции upsert или обновления (update) и вставить определенное значение по умолчанию при выполнении операции вставки (insert).<br/>– false: обновить данные в целевом объекте значением NULL при выполнении операции upsert или обновления (update) и вставить значение NULL при выполнении операции вставки (insert).  | Нет (значение по умолчанию — false) |

>[!NOTE]
>Значение по умолчанию writeBatchSize для приемника и значение [parallelCopies](copy-activity-performance.md#parallel-copy) для действия копирования в приемнике Dynamics равны 10, что означает одновременную отправку в Dynamics 100 записей.

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

При копировании данных из Dynamics для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных Dynamics. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

Настройте соответствующие типы данных для фабрики данных в структуре набора данных на основе вашего источника данных Dynamics типа с помощью приведенной ниже таблице сопоставления:

| Тип данных Dynamics | Тип промежуточных данных фабрики данных | Поддерживается в качестве источника | Поддерживается в качестве приемника |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | длинное целое | ✓ | ✓ |
| AttributeTypeCode.Boolean | Логическое | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Строка | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | Логическое | ✓ |  |
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

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.