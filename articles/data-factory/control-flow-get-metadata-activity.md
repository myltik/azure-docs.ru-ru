---
title: "Действие получения метаданных в фабрике данных Azure | Документация Майкрософт"
description: "Узнайте, как с помощью действия хранимой процедуры SQL Server можно вызвать хранимую процедуру в Базе данных SQL Azure или хранилище данных SQL Azure из конвейера фабрики данных."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Действие получения метаданных в фабрике данных Azure
Действие GetMetadata можно использовать для получения метаданных любых данных в фабрике данных Azure. Это действие поддерживается только для фабрик данных версии 2. Его можно использовать в следующих сценариях:

- Проверка информации о метаданных любых данных.
- Активация конвейера, когда данные готовы или доступны.

В потоке управления доступны следующие функциональные возможности:
- Выходные данные действия GetMetadata можно использовать в условных выражениях для выполнения проверки.
- Конвейер может сработать, если условие выполняется в цикле Do-Until.

Действие GetMetadata принимает набор данных в качестве необходимых входных данных и выводит сведения о метаданных в качестве выходных данных. В настоящее время поддерживается только набор данных большого двоичного объекта Azure. Поддерживаемые поля метаданных: size (размер), structure (структура) и lastModified (время последнего изменения).  

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью [Introduction to Azure Data Factory](v1/data-factory-introduction.md) (Введение в фабрику данных Azure).


## <a name="syntax"></a>Синтаксис

### <a name="get-metadata-activity-definition"></a>Определение действия получения метаданных
В следующем примере действие GetMetadata возвращает метаданные о данных, представленных набором MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Определение набора данных

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Выходные данные
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Свойства типа
В настоящее время действие GetMetadata может получать следующие типы сведений о метаданных из набора данных хранилища Azure.

Свойство | Описание | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
fieldList | Содержит типы необходимых сведений о метаданных.  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    Нет<br/>Если не указано, действие возвращает все 3 типа поддерживаемых сведений о метаданных. 
набор данных | Эталонный набор данных, действие которого требуется получить с помощью действия GetMetadata. <br/><br/>В настоящее время в качестве типа набора данных поддерживается большой двоичный объект Azure. Существуют два следующих подсвойства: <ul><li><b>referenceName</b>: ссылка на существующий набор данных большого двоичного объекта Azure.</li><li><b>type</b>: так как существует ссылка на набор данных, используется тип DatasetReference.</li></ul> |    <ul><li>Строка</li><li>DatasetReference</li></ul> | Да

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
