---
title: "Перемещение данных из базы данных MongoDB с помощью фабрики данных | Документация Майкрософт"
description: "Узнайте, как перемещать данные из базы данных MongoDB с использованием фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 2a590795178d819d9df1b010dc68809492d6bdbc


---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Перемещение данных из MongoDB с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из локальной базы данных MongoDB в другое хранилище данных. В основе этой статьи лежат сведения из статьи о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и о сочетаниях хранилищ данных (источника и приемника), поддерживаемых этих действием.

Служба фабрики данных поддерживает подключение к локальным источникам MongoDB с помощью шлюза управления данными. Сведения о шлюзе управления данными см. в статье [Шлюз управления данными](data-factory-data-management-gateway.md), а пошаговые инструкции по настройке шлюза для перемещения данных с использованием конвейера — в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

> [!NOTE]
> Шлюз используется для подключения к источнику MongoDB, даже если он размещен на виртуальных машинах IaaS Azure. Если вы подключаетесь к экземпляру MongoDB, размещенному в облаке, экземпляр шлюза можно тоже установить на виртуальную машину IaaS.
>
>

Сейчас фабрика данных поддерживает перемещение данных из MongoDB в другие хранилища данных, но не наоборот.

## <a name="supported-versions"></a>Поддерживаемые версии
Этот соединитель MongoDB поддерживает MongoDB версии 2.4 и выше.

## <a name="prerequisites"></a>Предварительные требования
Чтобы служба фабрики данных Azure могла подключаться к локальной базе данных MongoDB, необходимо установить следующие компоненты.

* Шлюз управления данными версии 2.0 или выше на том же компьютере, на котором размещена база данных, или на отдельном компьютере во избежание конкуренции за ресурсы. Шлюз управления данными — это программное обеспечение, которое обеспечивает безопасное и управляемое подключение локальных источников данных к облачным службам. Дополнительные сведения о шлюзе управления данными см. в статье [Шлюз управления данными](data-factory-data-management-gateway.md).

    Вместе со шлюзом автоматически устанавливается драйвер ODBC Microsoft MongoDB, который используется для подключения к базе данных MongoDB.

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из базы данных MongoDB в любое поддерживаемое хранилище-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные из базы данных MongoDB в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.

## <a name="sample-copy-data-from-mongodb-to-azure-blob"></a>Пример копирования данных из MongoDB в большой двоичный объект Azure
В этом примере показано, как скопировать данные из локальной базы данных MongoDB в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesMongoDb](#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [MongoDbCollection](#dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [MongoDbSource](#copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из результата выполнения запроса к базе данных MongoDB каждый час копируются в большой двоичный объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Необходимые инструкции представлены в статье [Шлюз управления данными](data-factory-data-management-gateway.md).

**Связанная служба MongoDB**

```JSON
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Связанная служба хранения Azure**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Входной набор данных MongoDB** Если для параметра external задать значение true, фабрика данных воспримет эту таблицу как внешнюю, которая создана не в результате какого-либо действия в этой службе.

```JSON
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```JSON
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%M"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%d"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%H"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **MongoDbSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **query** , выбирает для копирования данные за последний час.

```JSON
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```

## <a name="linked-service-properties"></a>Свойства связанной службы
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе **OnPremisesMongoDB** .

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **OnPremisesMongoDb** |Да |
| server |IP-адрес или имя узла сервера MongoDB |Да |
| порт |TCP-порт, используемый сервером MongoDB для прослушивания клиентских подключений |Значение по умолчанию — 27017 (необязательно) |
| authenticationType |Укажите тип Basic или Anonymous |Да |
| Имя пользователя |Учетная запись пользователя для доступа к MongoDB |Да (если используется обычная проверка подлинности) |
| пароль |Пароль для пользователя |Да (если используется обычная проверка подлинности) |
| authSource |Имя базы данных MongoDB, которое будет использоваться для проверки учетных данных при проверке подлинности |Необязательно (если используется обычная проверка подлинности). По умолчанию используется учетная запись администратора и база данных, указанная с помощью свойства databaseName |
| databaseName |Имя базы данных MongoDB, к которой необходимо получить доступ |Да |
| gatewayName |Имя шлюза, который обращается к хранилищу данных |Да |
| encryptedCredential |Учетные данные, зашифрованные шлюзом |Необязательно |

Дополнительные сведения о настройке учетных данных для локального источника данных MongoDB см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="dataset-type-properties"></a>Свойства типа "Набор данных"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных типа **MongoDbCollection** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| collectionName |Имя коллекции в базе данных MongoDB |Да |

## <a name="copy-activity-type-properties"></a>Свойства типа "Действие копирования"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Если источник относится к типу **MongoDbSource** , в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| query |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL-92. Например, select * from MyTable. |Нет (если для свойства **collectionName** задано значение **dataset**). |

## <a name="schema-by-data-factory"></a>Схема фабрики данных
Служба фабрики данных Azure определяет схему для коллекции MongoDB, используя последние 100 документов в коллекции. Если эти 100 документов не содержат полной схемы, во время копирования некоторые столбцы могут игнорироваться.

## <a name="type-mapping-for-mongodb"></a>Сопоставление типов для MongoDB
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа.

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в MongoDB, для преобразования типов MongoDB в типы .NET используются следующие сопоставления.

| Тип MongoDB | Тип .NET Framework |
| --- | --- |
| Binary |Byte[] |
| Логический |Логический |
| Дата |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Строковый |
| Строковый |Строковый |
| UUID |Guid |
| Объект |Преобразованный в плоские столбцы с "_" в качестве вложенного разделителя |

> [!NOTE]
> Дополнительные сведения о поддержке массивов с помощью виртуальных таблиц см. в разделе [Поддержка сложных типов с помощью виртуальных таблиц](#support-for-complex-types-using-virtual-tables) ниже.
>
>

В настоящее время не поддерживаются следующие типы MongoDB: DBPointer, JavaScript, мин. и макс. значение ключей, регулярное выражение, символ, метка времени, не определенный.

## <a name="support-for-complex-types-using-virtual-tables"></a>Поддержка сложных типов с помощью виртуальных таблиц
В фабрике данных Azure используется встроенный драйвер ODBC, который позволяет подключаться к базе данных MongoDB и копировать из нее данные. Для сложных типов, таких как массивы или объекты с различными типами данных в документах, драйвер ренормализует данные в соответствующие виртуальные таблицы. В частности, если таблица содержит такие столбцы, драйвер создает следующие виртуальные таблицы.

* **Базовая таблица**, в которой содержатся те же данные, что и в исходной таблице, кроме столбцов сложного типа. Имена базовой таблицы и таблицы, которую она представляет, совпадают.
* **Виртуальная таблица** для каждого столбца сложного типа, в которой представлены вложенные данные в развернутом виде. Виртуальным таблицам присваиваются имена в следующем формате: имя исходной таблицы, разделитель "_", имя массива или объекта.

Виртуальные таблицы ссылаются на данные в исходной таблице, что позволяет драйверу подключаться к денормализованным данным. Дополнительные сведения см. в разделе "Примеры". Доступ к содержимому массивов MongoDB можно получить при помощи отправки запроса и соединения виртуальных таблиц.

Просмотреть список таблиц, в том числе виртуальных, в базе данных MongoDB и содержащихся в них данных можно с помощью [мастера копирования](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) . Он также позволяет создать запрос и просмотреть результат выполнения.

### <a name="example"></a>Пример
Ниже приведен пример таблицы ExampleTable в базе данных MongoDB, содержащей столбец ("Счета") с массивом объектов в каждой ячейке и столбец с массивом данных скалярных типов ("Оценки").

| _№ | Имя клиента | Счета | Уровень обслуживания | Оценки |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{счет_№:"123", товар:"тостер", цена:"456", скидка:"0,2"}, {счет_№:"124", товар:"печь", цена: "1235", скидка: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{счет_№: "135", товар: "холодильник", цена: "12543", скидка: "0,0"}] |Gold |[1,2] |

Для представления такой одной таблицы драйвер создает несколько виртуальных таблиц. Ниже приведен пример первой базовой виртуальной таблицы ExampleTable. Базовая таблица содержит все данные из исходной таблицы, но данные массивов опущены и развернуты в виртуальных таблицах.

| _№ | Имя клиента | Уровень обслуживания |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

В следующих таблицах представлены виртуальные таблицы, соответствующие исходным массивам в примере. Каждая из этих таблиц содержит следующее:

* обратную ссылку на исходный ключевой столбец, соответствующий строке исходного массива (с помощью столбца _№);
* указание на позицию данных в исходном массиве;
* развернутые данные для каждого элемента в массиве.

Таблица ExampleTable_Invoices:

| _№ | ExampleTable_Invoices_dim1_idx | счет_№ | item | price | Скидка |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |тостер |456 |0,2 |
| 1111 |1 |124 |печь |1235 |0,2 |
| 2222 |0 |135 |холодильник |12543 |0,0 |

Таблица ExampleTable_Ratings:

| _№ | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со статьей [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md), в которой представлены пошаговые инструкции по созданию конвейера данных, который перемещает данные из локального хранилища в хранилище данных Azure.



<!--HONumber=Nov16_HO3-->


