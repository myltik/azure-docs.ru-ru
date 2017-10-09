---
title: "Копирование данных в индекс службы \"Поиск\" с использованием фабрики данных | Документация Майкрософт"
description: "Узнайте, как отправлять или копировать данные в индекс службы \"Поиск Azure\" с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ae4537ee37103392f78c4fa554984f1375634f17
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Копирование данных в индекс службы "Поиск Azure" с использованием фабрики данных Azure

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-azure-search-connector.md)
> * [Версия 2 — предварительная](connector-azure-search.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в индекс службы "Поиск Azure". Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Push data to an Azure Search index by using Azure Data Factory](v1/data-factory-azure-search-connector.md) (Перемещение данных в индекс службы "Поиск Azure" с помощью фабрики данных Azure).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Данные можно копировать из любых поддерживаемых исходных хранилищ данных в индекс службы "Поиск Azure". Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю службы "Поиск Azure".

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы "Поиск Azure" поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureSearch**. | Да |
| URL-адрес | URL-адрес службы Поиска Azure. | Да |
| key | Ключ администратора службы Поиска Azure. Пометьте это поле в качестве SecureString. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

> [!IMPORTANT]
> При копировании данных из облачного хранилища данных в индекс связанной службы "Поиск Azure" необходимо обратиться к интегрированной среде выполнения Azure, явно задав регион в connactVia. Задайте регион, в котором находится служба "Поиск Azure". Дополнительные сведения см. в разделе "Интегрированная среда выполнения Azure" (concepts-integration-runtime.md#azure-integration-runtime).

**Пример**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Поиска Azure.

Чтобы скопировать данные в службу "Поиск Azure", присвойте свойству type набора данных значение **RelationalTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение: **AzureSearchIndex**. | Да |
| indexName | Имя индекса Поиска Azure. Фабрика данных не создает индекс. Индекс должен существовать в Поиске Azure. | Да |

**Пример**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником службы "Поиск Azure".

### <a name="azure-search-as-sink"></a>Служба "Поиск Azure" в качестве приемника

Чтобы скопировать данные в службу "Поиск Azure", в действии копирования задайте тип источника **AzureSearchIndexSink**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **AzureSearchIndexSink**. | Да |
| WriteBehavior | Указывает действие (объединение или замена), выполняемое, если документ уже существует в индексе. Ознакомьтесь с разделом [Свойство WriteBehavior](#writebehavior-property).<br/><br/>Допустимые значения: **Объединить** (по умолчанию) и **Отправить**. | Нет |
| WriteBatchSize | Передает данные в индекс Поиска Azure, когда размер буфера достигает значения writeBatchSize. Ознакомьтесь с разделом [Свойство WriteBatchSize](#writebatchsize-property).<br/><br/>Допустимые значения: целые числа от 1 до 1000; значение по умолчанию — 1000. | Нет |

### <a name="writebehavior-property"></a>Свойство WriteBehavior

При записи данных AzureSearchSink выполняет операцию upsert. Другими словами, если при создании документа ключ документа уже существует в индексе Поиска Azure, Поиск Azure обновляет существующий документ, а не создает исключения из-за конфликта.

AzureSearchSink проявляет два типа поведения upsert (с использованием пакета SDK AzureSearch):

- **Объединение**. Все столбцы в новом документе объединяются со столбцами в существующем. Для столбцов с значением null в новом документе значение столбцов в существующем документе сохраняется.
- **Отправка**. Новый документ заменяет существующий. Для столбцов, не указанных в новом документе, задается значение null независимо от того, есть ли в существующем документе столбцы с значением, отличным от null.

Поведение по умолчанию — **объединение**.

### <a name="writebatchsize-property"></a>Свойство WriteBatchSize

Служба Поиска Azure поддерживает запись документов в виде пакета. Пакет может содержать от 1 до 1000 действий. Действие обрабатывает один документ для выполнения операции передачи или объединения.

**Пример**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "WriteBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Поддержка типов данных

В следующей таблице представлены сведения о поддержке типов данных Поиска Azure.

| Тип данных Поиска Azure | Поддерживается в приемнике Поиска Azure |
| ---------------------- | ------------------------------ |
| Строка | Да |
| Int32 | Да |
| Int64 | Да |
| Double | Да |
| Логический | Да |
| DataTimeOffset | Да |
| Массив строк | Нет |
| GeographyPoint | Нет |

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
