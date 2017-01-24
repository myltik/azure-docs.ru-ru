---
title: "Отправка данных в индекс поиска с использованием фабрики данных | Документация Майкрософт"
description: "Узнайте о том, как передавать данные в индекс Поиска Azure с использованием фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 55c988bf74ff0f2e519e895a735dc68f3dc99855
ms.openlocfilehash: e2deed13106db9467eef181f25a0a226034df5a2

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Передача данных в индекс Поиска Azure с использованием фабрики данных Azure
В этой статье описывается, как использовать действие копирования для передачи данных из локального хранилища данных, которое поддерживает служба фабрики данных, в индекс Поиска Azure. Поддерживаемые исходные хранилища данных перечислены в столбце "Источник" таблицы [поддерживаемых источников и приемников](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## <a name="enabling-connectivity"></a>Включение соединения
Чтобы разрешить подключение службы фабрики данных к локальному хранилищу данных, установите в локальной среде шлюз управления данными. Его можно установить на том же компьютере, на котором размещено исходное хранилище данных, или на отдельном компьютере во избежание конкуренции за ресурсы с хранилищем данных.

Шлюз управления данными обеспечивает безопасное и управляемое подключение локальных источников данных к облачным службам. Сведения о шлюзе управления данными см. в статье [Перемещение данных между локальными источниками и облаком](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="copy-data-wizard"></a>Мастер копирования данных
Проще всего создать конвейер, копирующий данные из любого поддерживаемого исходного хранилища данных в Поиск Azure, с помощью мастера копирования данных. Краткое пошаговое руководство представлено в статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md).

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные из локального сервера SQL Server в индекс Поиска Azure. Однако данные можно копировать из любого указанного [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) локального хранилища данных, используя действие копирования в фабрике данных Azure.   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Пример: копирование данных из локального сервера SQL Server в индекс Поиска Azure

В примере ниже используется следующее:

1.  Связанная служба типа [AzureSqlDW](#azure-search-linked-service-properties).
2.  Связанная служба типа [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
3.  Входной [набор данных](data-factory-create-datasets.md) типа [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.  Выходной [набор данных](data-factory-create-datasets.md) типа [AzureSearchIndex](#azure-search-index-dataset-properties).
4.  [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) и [AzureSearchIndexSink](#azure-search-index-sink-properties).

В этом примере каждый час копируются данные временных рядов из локальной базы данных SQL Server в индекс Поиска Azure. Используемые в этом примере свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными на локальном компьютере. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

**Связанная служба Поиска Azure:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Связанная служба SQL Server**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Входной набор данных SQL Server**

В примере предполагается, что вы уже создали таблицу MyTable в SQL Server и она содержит столбец с именем timestampcolumn для данных временного ряда. Можно выполнить запрос к нескольким таблицам в одной базе данных, используя один набор данных, но для typeProperty tableName набора данных нужно указать одну таблицу.

Если для параметра external задать значение true, то фабрика данных воспримет этот набор данных как внешний, который создан не в результате какого-либо действия в этой службе.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Выходной набор данных Поиска Azure:**

В примере данные копируются в индекс Поиска Azure с именем **products**. Фабрика данных не создает индекс. Чтобы проверить пример, создайте индекс с таким именем. Создайте индекс Поиска Azure с таким же количеством столбцов, что и во входном наборе данных. Новые записи добавляются в индекс Поиска Azure каждый час.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для свойства type параметра **source** установлено значение **SqlSource**, а для свойства type параметра **sink** — значение **AzureSearchIndexSink**. SQL-запрос, указанный для свойства **SqlReaderQuery** , выбирает для копирования данные за последний час.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```

При копировании данных из облачного хранилища данных в Поиск Azure свойство `executionLocation` является обязательным. Ниже в качестве примера показано изменение, необходимое в действии копирования `typeProperties`. Дополнительные сведения и поддерживаемые значения см. в разделе [Копирование данных из одного облачного хранилища данных в другое](data-factory-data-movement-activities.md#global).

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="azure-search-linked-service-properties"></a>Свойства связанной службы Поиска Azure

В таблице ниже приведены описания элементов JSON, которые относятся к связанной службе Поиска Azure.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| type | Для свойства type необходимо задать значение **AzureSearch**. | Да |
| URL-адрес | URL-адрес службы Поиска Azure. | Да |
| key | Ключ администратора службы Поиска Azure. | Да |

## <a name="azure-search-index-dataset-properties"></a>Свойства набора данных индекса Поиска Azure

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных. Разделы **typeProperties** для каждого типа набора данных отличаются. Раздел typeProperties для набора данных типа **AzureSearchIndex** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| type | Для свойства type необходимо задать значение **AzureSearchIndex**| Да |
| indexName | Имя индекса Поиска Azure. Фабрика данных не создает индекс. Индекс должен существовать в Поиске Azure. | Да |


## <a name="azure-search-index-sink-properties"></a>Свойства приемника индекса Поиска Azure
Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure: создание конвейеров, цепочки действий и расписаний для них](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий. С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Для действия копирования, когда источник относится к типу **AzureSearchIndexSink**, в разделе typeProperties доступны указанные ниже свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Указывает действие (объединение или замена), выполняемое, если документ уже существует в индексе. Ознакомьтесь с разделом [Свойство WriteBehavior](#writebehavior-property).| Merge (по умолчанию)<br/>Отправить| Нет |
| WriteBatchSize | Передает данные в индекс Поиска Azure, когда размер буфера достигает значения writeBatchSize. Ознакомьтесь с разделом [Свойство WriteBatchSize](#writebatchsize-property). | 1–1000. Значение по умолчанию — 1000. | Нет |

### <a name="writebehavior-property"></a>Свойство WriteBehavior
При записи данных AzureSearchSink выполняет операцию upsert. Другими словами, если при создании документа ключ документа уже существует в индексе Поиска Azure, Поиск Azure обновляет существующий документ, а не создает исключения из-за конфликта.

AzureSearchSink проявляет два типа поведения upsert (с использованием пакета SDK AzureSearch):

- **Объединение**. Все столбцы в новом документе объединяются со столбцами в существующем. Для столбцов с значением null в новом документе значение столбцов в существующем документе сохраняется.
- **Отправка**. Новый документ заменяет существующий. Для столбцов, не указанных в новом документе, задается значение null независимо от того, есть ли в существующем документе столбцы с значением, отличным от null.

Поведение по умолчанию — **объединение**.

### <a name="writebatchsize-property"></a>Свойство WriteBatchSize
Служба Поиска Azure поддерживает запись документов в виде пакета. Пакет может содержать от 1 до 1000 действий. Действие обрабатывает один документ для выполнения операции передачи или объединения.

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

## <a name="copy-from-a-cloud-source"></a>Копирование из облачного источника
При копировании данных из облачного хранилища данных в Поиск Azure свойство `executionLocation` является обязательным. Ниже в качестве примера показано изменение, необходимое в действии копирования `typeProperties`. Дополнительные сведения и поддерживаемые значения см. в разделе [Копирование данных из одного облачного хранилища данных в другое](data-factory-data-movement-activities.md#global).

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Указание определения структуры для прямоугольных наборов данных
Раздел structure в JSON наборов данных, содержащий коллекцию столбцов для таблицы, является **необязательным** для прямоугольных таблиц (со строками и столбцами). Используйте раздел structure либо для указания сведений о типе при преобразовании типов, либо при сопоставлении столбцов. В следующих разделах эти функции описаны более подробно.

У каждого столбца есть приведенные ниже свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| name | Имя столбца. | Да |
| type | Тип данных столбца. Дополнительные сведения о том, когда следует указывать данные о типе, см. в разделе о [преобразовании типов](#type-conversion-sample). | Нет |
| culture | Язык и региональные параметры на основе .NET, используемые при указании типа, если это тип .NET `Datetime` или `Datetimeoffset`. Значение по умолчанию — `en-us`.  | Нет |
| свойства | Строка формата, используемая при указании типа, если это тип .NET `Datetime` или `Datetimeoffset`. | Нет |

В следующем примере показан JSON раздела structure для таблицы, в которой имеются три столбца — `userid`, `name` и `lastlogindate`.

```JSON
"structure":
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
Используйте приведенные ниже рекомендации о том, когда следует включать сведения о структуре и что включать в раздел **structure**.

- **Для структурированных источников данных**, в которых наряду с данными хранятся схема данных и сведения о типах (например, SQL Server, Oracle, таблица Azure и т. п.), раздел structure следует указывать только при сопоставлении определенных исходных столбцов с конкретными столбцами в приемнике, если их имена различаются. Дополнительные сведения см. в разделе сопоставления столбцов.

    Как было упомянуто ранее, сведения о типах являются необязательными в разделе structure. Для структурированных источников сведения о типах доступны в определении набора данных в хранилище данных, поэтому их не следует включать при добавлении раздела structure.
- **Для схемы на основе считываемых источников данных (а именно большого двоичного объекта Azure)** вы можете выбрать хранение данных без сохранения какой-либо схемы или сведений о типах вместе с ними. Для таких типов источников данных раздел structure нужно добавлять в следующих двух случаях:
    - При сопоставлении исходных столбцов со столбцами приемника.
    - Если в действии копирования набор данных является источником, сведения о типах можно указать в разделе structure. Фабрика данных использует эти сведения о типах для преобразования в собственные типы для приемника. Дополнительные сведения см. в статье [Перемещение данных в большой двоичный объект Azure и из него с помощью фабрики данных Azure](data-factory-azure-blob-connector.md).

### <a name="supported-net-based-types"></a>Поддерживаемые типы на основе .NET
Фабрика данных поддерживает следующие CLS-совместимые значения типов на основе .NET, когда нужно указывать сведения о типах в разделе structure для схемы по считываемым источникам данных, таким как большие двоичные объекты Azure.

- Int16
- Int32
- Int64
- Single
- Double
- Decimal
- Bool
- Строка
- Datetime
- Datetimeoffset
- Timespan

Чтобы упростить разбор пользовательской строки Datetime, для типов Datetime и Datetimeoffset можно при необходимости также указать строки culture и format. Пример преобразования типа см. в следующем разделе.


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Производительность и настройка  
Сведения о ключевых факторах, влияющих на производительность перемещения данных (действие копирования), и различных способах оптимизации этого процесса см. в статье [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

* [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .



<!--HONumber=Dec16_HO3-->


