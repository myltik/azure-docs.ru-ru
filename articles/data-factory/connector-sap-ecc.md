---
title: Копирование данных из SAP ECC с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из SAP ECC в поддерживаемые хранилища данных, используемые в качестве приемника, с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: 95b0440dbec1a13096928bdefd31383d07a1f86d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618578"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Копирование данных из SAP ECC с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из SAP ECC (SAP Enterprise Central Component). Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из SAP ECC можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP ECC поддерживает:

- копирование данных из SAP ECC в SAP NetWeaver 7.0 и более поздних версий; 
- копирование данных из любых объектов, предоставляемых службами SAP ECC OData (например, таблиц и представлений SAP, BAPI, средств извлечения данных и т. д.), или данных и документов IDOC, отправляемых в SAP PI, которые могут быть получены как OData через относительные адаптеры;
- Копирование данных с помощью базовой проверки подлинности.

## <a name="prerequisites"></a>предварительным требованиям

Как правило, SAP ECC предоставляет сущности через службы OData посредством шлюза SAP. Чтобы использовать этот соединитель SAP ECC, сделайте следующее:

- **Настройте шлюз SAP**. Для серверов с SAP NetWeaver версии выше 7.4 шлюз SAP уже установлен. В противном случае необходимо установить встроенный шлюз или концентратор шлюзов, чтобы предоставить данные SAP ECC через службы OData. Сведения о том, как настроить шлюз SAP, см. в [руководстве по установке](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Активируйте и настройте службу SAP OData**. Активировать службы OData можно через TCODE SICF за считаные секунды. Также можно указать, какие объекты требуется предоставлять. Ниже приведен пример [пошагового руководства](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP ECC.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP ECC поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **SapEcc** | Yes |
| URL-адрес | URL-адрес службы SAP ECC OData. | Yes |
| Имя пользователя | Имя пользователя, применяемое для подключения к SAP ECC. | Нет  |
| password | Пароль (открытым текстом), применяемый для подключения к SAP ECC. | Нет  |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

**Пример.**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных SAP ECC.

Чтобы скопировать данные из SAP ECC, задайте для свойства type набора данных значение **SapEccResource**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| path | Путь к сущности SAP ECC OData. | Yes |

**Пример**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC в качестве источника

Чтобы копировать данные из SAP ECC, задайте для типа источника в действии копирования значение **SapEccSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **SapEccSource**. | Yes |
| query | Параметры запроса OData для фильтрации данных. Пример: $select=Name,Description&$top=10.<br/><br/>Соединитель SAP ECC копирует данные из комбинированного URL-адреса: (URL-адрес, указанный в связанной службе)/(путь, указанный в наборе данных)?(запрос, указанный в источнике действия копирования). См. статью о [компонентах URL-адреса OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Нет  |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>Сопоставление типов данных для SAP ECC

При копировании данных из SAP ECC используются следующие сопоставления типов данных OData для данных SAP ECC с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных OData | Тип промежуточных данных фабрики данных |
|:--- |:--- |:--- |
| Edm.Binary | Строка |
| Edm.Boolean | Bool |
| Edm.Byte | Строка |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Строка |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Строка |
| Edm.Time | Интервал времени |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Данные сложных типов сейчас не поддерживаются.

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
