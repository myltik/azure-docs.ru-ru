---
title: "Скопировать данные из/облако SAP для клиента с использованием фабрики данных Azure | Документы Microsoft"
description: "Сведения о копировании данных из облака SAP для клиента на поддерживаемых приемника хранилища данных (или) из поддерживаемой исходной хранилищ данных SAP облако для клиента с помощью фабрики данных."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ad3bc7ba38f5ea20586031bdcc3ae44f03f9da0b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Копирование данных из облака SAP для клиента (C4C), с помощью фабрики данных Azure

В этой статье описаны способы использования действия копирования в фабрике данных Azure для копирования данных из или в облаке SAP для клиента (C4C). Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Копирование данных из облака SAP для клиента в хранилище данных любой поддерживаемый приемник или копирования данных из любого хранилища, поддерживаемого источника данных SAP облако для клиента. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности этот соединитель обеспечивает фабрики данных Azure для копирования данных из или в облаке SAP для клиента, включая облачные SAP для продаж, облако SAP для службы и облака SAP для социальных сетей Engagement решений.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы предоставляют подробные сведения о свойствах, которые используются для определения объекты фабрики данных SAP облако для соединителя клиента.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для службы клиента, связанного облака SAP поддерживает следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **SapCloudForCustomer**. | Yes |
| URL-адрес | URL-адрес службы SAP C4C OData. | Yes |
| Имя пользователя | Укажите имя пользователя для подключения к SAP C4C. | Yes |
| password | Укажите пароль для учетной записи, указанной для имени пользователя. Пометьте это поле в качестве SecureString. | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника |

>[!IMPORTANT]
>Для копирования данных в облаке SAP для клиента, явно [создать IR Azure](create-azure-integration-runtime.md#create-azure-ir) с в местоположение рядом облако SAP для клиента и сопоставления в связанной службе, как в следующем примере:

**Пример.**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых SAP облака для набора данных клиента.

Чтобы скопировать данные из облака SAP для клиента, задайте для свойства типа набора данных, **SapCloudForCustomerResource**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа набора данных должно быть присвоено: **SapCloudForCustomerResource** |Yes |
| path | Укажите путь к сущности SAP C4C OData. |Yes |

**Пример.**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых SAP облака для клиента источника.

### <a name="sap-c4c-as-source"></a>C4C SAP в качестве источника

Чтобы скопировать данные из облака SAP для клиента, задайте тип источника в действии копирования для **SapCloudForCustomerSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **SapCloudForCustomerSource**  | Yes |
| query | Укажите пользовательский запрос OData для чтения данных. | Нет  |

Образец запроса для получения данных для определенного дня.`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
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
                "type": "SAPC4CSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>C4C SAP в качестве приемника

Чтобы скопировать данные в облако SAP для клиента, необходимо задать тип приемника в действии копирования для **SapCloudForCustomerSink**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **SapCloudForCustomerSink**  | Yes |
| writeBehavior | Поведение операции при записи. Может быть «Вставить», «Обновления». | Нет. По умолчанию «Вставка». |
| writeBatchSize | Размер пакета для операции записи. Размер пакета для обеспечения высокой производительности может быть разным для различных таблиц или сервера. | Нет. По умолчанию 10. |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Сопоставление типов данных для облака SAP для клиента

При копировании данных из облака SAP для клиента, следующие сопоставления используются из облака SAP для типов данных клиента — для типов промежуточных данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных OData C4C SAP | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Строка |
| Edm.Time | Интервал времени |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
