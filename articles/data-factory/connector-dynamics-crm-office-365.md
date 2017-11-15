---
title: "Копирование данных из Dynamics CRM и 365 с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как копировать данные из Dynamics CRM и 365 на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Копирование данных из Dynamics CRM или Dynamics 365 с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Dynamics 365 или Dynamics CRM. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Dynamics 365 или Dynamics CRM можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Dynamics поддерживает следующие версии Dynamics и типы проверки подлинности:

| Версии Dynamics | Типы проверки подлинности | Примеры связанной службы |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Оffice 365 | [Проверка подлинности Dynamics Online и Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 (локальная версия) с IFD <br> Dynamics CRM 2016 (локальная версия) с IFD <br> Dynamics CRM 2015 (локальная версия) с IFD | IFD | [Dynamics (локальная версия) с IFD и проверка подлинности IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD — сокращение от термина "развертывание с выходом в Интернет".*

> [!NOTE]
> Чтобы использовать соединитель Dynamics, сохраните пароль в Azure Key Vault и позвольте действию копирования ADF совершать запросы при выполнении копирования данных. Дополнительные сведения о копировании см. в разделе [Свойства связанной службы](#linked-service-properties).

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Dynamics**. | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | Да |
| оrganizationName | Имя организации экземпляра Dynamics. | Нет, следует указывать при наличии нескольких экземпляров Dynamics, связанных с пользователем. |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Office365** для Dynamics Online. | Да |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Да |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Необходимо поместить пароль в Azure Key Vault и настроить его как AzureKeyVaultSecret. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 и Dynamics CRM (локальная версия) с IFD

*hostName и port являются дополнительными свойствами по сравнению с Dyanmics Online.*

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Dynamics**. | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD должно иметь значение **OnPremisesWithIfd**.| Да |
| **Имя узла** | Имя узла локального сервера Dynamics. | Да |
| **port** | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| оrganizationName | Имя организации экземпляра Dynamics. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | Да |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Да |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Обратите внимание, что необходимо поместить пароль в Azure Key Vault и настроить его как AzureKeyVaultSecret. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Dynamics.

Чтобы скопировать данные из Dynamics, установите свойство типа набора данных **DynamicsEntity**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **DynamicsEntity**. |Да |
| entityName | Логическое имя сущности, которое требуется получить. | Нет (если свойство query указано в источнике действия) |

> [!IMPORTANT]
> **Для Dynamics является обязательным раздел structure в наборе данных** , который определяет имя столбца и тип данных для данных Dynamics, которые требуется скопировать. Узнайте больше о [структуре набора данных](concepts-datasets-linked-services.md#dataset-structure) и [сопоставлении типов данных для Dynamics](#data-type-mapping-for-dynamics).

**Пример**

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Dynamics.

### <a name="dynamics-as-source"></a>Dynamics в качестве источника

Чтобы копировать данные из Dynamics, установите тип источника **DynamicsSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **DynamicsSource**.  | Да |
| query  | FetchXML — это защищаемый язык запросов, используемый в Microsoft Dynamics (Online, локальная версия). Узнайте больше о [создании запросов с помощью FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx), ознакомившись со следующим примером. | Нет (если для набора данных задано свойство entityName)  |

**Пример**

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

## <a name="data-type-mapping-for-dynamics"></a>Сопоставление типов данных для Dynamics

При копировании данных из Dynamics для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных Dynamics. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

Настройте соответствующие типы данных ADF в структуре набора данных на основе вашего типа данных источника Dynamics, используя приведенную ниже таблицу сопоставления:

| Тип данных Dynamics | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| AttributeTypeCode.BigInt | длинное целое |
| AttributeTypeCode.Boolean | Логический |
| AttributeType.Customer | Guid |
| AttributeType.DateTime | Datetime |
| AttributeType.Decimal | Decimal |
| AttributeType.Double | Double |
| AttributeType.EntityName | Строка |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | Guid |
| AttributeType.ManagedProperty | Логический |
| AttributeType.Memo | Строка |
| AttributeType.Money | Decimal |
| AttributeType.Owner | Guid |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | Guid |
| AttributeType.String | Строка |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Типы данных Dynamics AttributeType.CalendarRules и AttributeType.PartyList не поддерживаются.


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.