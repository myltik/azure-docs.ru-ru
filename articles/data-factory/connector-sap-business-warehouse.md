---
title: Копирование данных из SAP BW с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из SAP Business Warehouse на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: f5af46017dede0deac92ff5a7f35f17e083590d6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618612"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Копирование данных из SAP Business Warehouse с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-sap-business-warehouse-connector.md)
> * [Версия 2 — предварительная](connector-sap-business-warehouse.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из SAP Business Warehouse (BW). Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из SAP Business Warehouse с помощью фабрики данных Azure](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из SAP Business Warehouse можно скопировать в любое поддерживаемое хранилище данных приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель SAP Business Warehouse поддерживает:

- SAP Business Warehouse **версии 7.x**.
- Копирование данных из **InfoCubes и QueryCubes** (включая запросы BEx) с помощью запросов многомерных выражений.
- Копирование данных с помощью базовой проверки подлинности.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы использовать этот соединитель SAP Business Warehouse, сделайте следующее:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите **библиотеку SAP NetWeaver** на компьютере среды выполнения интеграции. Библиотеку SAP Netweaver можно получить у администратора SAP или непосредственно на странице [SAP Software Download Center](https://support.sap.com/swdc) (Центр загрузки программного обеспечения SAP). Найдите **примечание к SAP № 1025361**, чтобы узнать адрес для скачивания самой последней версии. Убедитесь, что выбрана **64-разрядная** библиотека SAP NetWeaver, которая соответствует установке интеграции среды выполнения. Установите все файлы, включенные в состав пакета SDK RFC для SAP NetWeaver, согласно примечанию к SAP. Библиотека SAP NetWeaver также включена в состав клиентских инструментов SAP.

> [!TIP]
> Поместите библиотеки DLL, извлеченные из пакета SDK RFC для NetWeaver, в папку system32.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP Business Warehouse.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP Business Warehouse (BW) поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **SapBw** | Yes |
| server | Имя сервера, на котором размещен экземпляр SAP Business Warehouse. | Yes |
| systemNumber | Номер системы SAP Business Warehouse.<br/>Допустимые значения: двузначное десятичное число, представленное в виде строки. | Yes |
| clientid | Идентификатор клиента в системе SAP Business Warehouse.<br/>Допустимые значения: трехзначное десятичное число, представленное в виде строки. | Yes |
| userName | Имя пользователя, имеющего доступ к серверу SAP. | Yes |
| password | Пароль для пользователя Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Yes |

**Пример.**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных SAP BW.

Чтобы скопировать данные из SAP BW, установите свойство type набора данных **RelationalTable**. Сейчас для набора данных SAP Business Warehouse типа RelationalTable не поддерживаются какие-либо свойства типа.

**Пример.**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW в качестве источника

Чтобы копировать данные из SAP BW, установите тип источника в действии копирования **RelationalSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **RelationalSource**. | Yes |
| query | Указывает запрос многомерных выражений для чтения данных из экземпляра SAP Business Warehouse. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Сопоставление типов для SAP BW

При копировании данных из SAP BW используются следующие сопоставления типов данных SAP BW с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных SAP BW | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| ACCP | int |
| CHAR | Строка |
| CLNT | Строка |
| CURR | Decimal |
| CUKY | Строка |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | Строка |
| LCHR | Строка |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | Строка |
| ЕДИНИЦА ИЗМЕРЕНИЯ | Строка |
| DATS | Строка |
| NUMC | Строка |
| TIMS | Строка |


## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
