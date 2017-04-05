---
title: "Перемещение данных из источника HTTP в Azure | Документация Майкрософт"
description: "Сведения о перемещении данных из локального или облачного источника HTTP с помощью фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d01dd1fdeed4aa809ba5118f79968f7719b11865
ms.lasthandoff: 03/27/2017


---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Перемещение данных из источника HTTP с помощью фабрики данных Azure
В этой статье описано, как с помощью действия копирования в фабрике данных Azure переместить данные из локальной или облачной конечной точки HTTP в поддерживаемый приемник данных. В этой статье в продолжение темы о [действиях перемещения данных](data-factory-data-movement-activities.md) приведены общие сведения о перемещении данных с помощью действия копирования, а также список поддерживаемых хранилищ данных, используемых в качестве источников и приемников.

В настоящее время фабрика данных поддерживает только перемещение данных из источника HTTP в другие хранилища данных, но не наоборот.

## <a name="supported-scenarios-and-authentication-types"></a>Поддерживаемые сценарии и типы аутентификации
Вы можете использовать этот соединитель HTTP, чтобы получить данные из **облачной и локальной конечных точек HTTP** с помощью метода HTTP **GET** или **POST**. Поддерживаемые типы аутентификации: **Anonymous**, **Basic**, **Digest**, **Windows** и **ClientCertificate**. Разница между этим соединителем и [соединителем для веб-таблиц](data-factory-web-table-connector.md) заключается в том, что соединитель для веб-таблиц используется для извлечения содержимого таблицы из веб-страницы HTML.

При копировании данных из локальной конечной точки HTTP необходимо установить шлюз управления данными в локальной среде или на виртуальной машине Azure. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с действием копирования, которое перемещает данные из источника HTTP с помощью разных инструментов и интерфейсов API.

- Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

- Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Примеры JSON для копирования данных из источника HTTP в хранилище BLOB-объектов Azure см. в разделе [Примеры определений JSON](#json-examples).

## <a name="linked-service-properties"></a>Свойства связанной службы
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе HTTP.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type | Задайте для этого свойства значение `Http`. | Да |
| url | Базовый URL-адрес веб-сервера. | Да |
| authenticationType | Указывает тип проверки подлинности. Допустимые значения: **Anonymous**, **Basic**, **Digest**, **Windows** и **ClientCertificate**. <br><br> В разделах ниже описываются дополнительные свойства и приведены примеры JSON для поддерживаемых типов проверки подлинности. | Да |
| enableServerCertificateValidation | Укажите, следует ли включать проверку SSL-сертификата на сервере, если источником является веб-сервер HTTPS. | Нет. Значение по умолчанию — true. |
| gatewayName | Имя шлюза управления данными для подключения к локальному источнику HTTP. | Да, если копирование выполняется из локального источника HTTP. |
| encryptedCredential | Зашифрованные учетные данные для доступа к конечной точке HTTP. При настройке сведений для проверки подлинности в мастере копирования или всплывающем диалоговом окне ClickOnce создаются автоматически. | Нет. Применимо, только когда копирование данных выполняется с локального HTTP-сервера. |

Дополнительные сведения о настройке учетных данных для локального источника данных соединителя HTTP см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Использование типов проверки подлинности Basic, Digest или Windows

Задайте для свойства `authenticationType` значения `Basic`, `Digest` или `Windows` и укажите следующие свойства помимо универсальных свойств соединителя HTTP, приведенных выше.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| Имя пользователя | Имя пользователя для доступа к конечной точке HTTP. | Да |
| password | Пароль пользователя, указанного в свойстве имя пользователя. | Да |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Пример: использование типов проверки подлинности Basic, Digest или Windows

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Использование типа проверки подлинности ClientCertificate

Чтобы использовать базовую проверку подлинности, задайте для `authenticationType` значение `ClientCertificate` и укажите приведенные ниже свойства помимо универсальных свойств соединителя HTTP, которые описаны выше.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| embeddedCertData | Содержимое двоичных данных файла обмена личной информацией (PFX-файла) с кодировкой Base64. | Должно быть указано одно из свойств: `embeddedCertData` или `certThumbprint`. |
| certThumbprint | Отпечаток сертификата, который был установлен в хранилище сертификатов на компьютере шлюза. Применимо, только когда копирование данных выполняется из локального источника HTTP. | Должно быть указано одно из свойств: `embeddedCertData` или `certThumbprint`. |
| password | Пароль, связанный с сертификатом. | Нет |

Если вы используете свойство `certThumbprint` для проверки подлинности, а сертификат установлен в личном хранилище локального компьютера, вам необходимо предоставить службе шлюза разрешение на чтение.

1. Запустите консоль управления (MMC). Добавьте оснастку **Сертификаты**, которая связана с **локальным компьютером**.
2. Разверните **Сертификаты**, **Личные**, а затем щелкните **Сертификаты**.
3. Щелкните правой кнопкой мыши сертификат в личном хранилище, а затем выберите **Все задачи**->**Управление закрытыми ключами...**
3. На вкладке **Безопасность** добавьте учетную запись пользователя, под которой запущена служба узла шлюза управления данными с доступом на чтение к сертификату.  

#### <a name="example-using-client-certificate"></a>Пример: использование сертификата клиента
Эта связанная служба связывает фабрику данных с локальным веб-сервером HTTP. Она использует сертификат клиента, установленный на компьютере со шлюзом управления данными.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Пример: использование сертификата клиента в файле
Эта связанная служба связывает фабрику данных с локальным веб-сервером HTTP. Она использует файл сертификата клиента на компьютере, где установлен шлюз управления данными.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties для набора данных типа **Http** имеет следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Тип набора данных. Нужно задать значение `Http`. | Да |
| relativeUrl | Относительный URL-адрес ресурса, который содержит данные. Если путь не задан, используется только URL-адрес, указанный в определении связанной службы. | Нет |
| requestMethod | Метод HTTP. Допустимые значения: **GET** или **POST**. | Нет. Значение по умолчанию — `GET`. |
| additionalHeaders | Дополнительные заголовки HTTP-запроса. | Нет |
| requestBody | Текст HTTP-запроса. | Нет |
| partitionedBy | Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке folderPath каждый час будет другим. | Нет |
| свойства | Если вы хотите просто **извлечь данные из конечной точки HTTP "как есть"** — без анализа, пропустите параметры форматирования. <br><br> Поддерживаемые форматы для выполнения анализа содержимого ответа HTTP в процессе выполнения операции копирования: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](#specifying-textformat), [формате Json](#specifying-jsonformat), [формате Avro](#specifying-avroformat), [формате Orc](#specifying-orcformat) и [ формате Parquet](#specifying-parquetformat).  |Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. Дополнительные сведения см. в разделе [Указание сжатия](#specifying-compression). |Нет |

### <a name="example-using-the-get-default-method"></a>Пример: использование метода GET (по умолчанию)

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>Пример: использование метода POST

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-properties"></a>Свойства действия копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

В настоящее время, если источник в действии копирования относится к типу **Http**, поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| httpRequestTimeout | Время ожидания ответа для HTTP-запроса. Это интервал времени для получения ответа, а не считывания данных ответа. | Нет. Значение по умолчанию  — 00:01:40. |

## <a name="json-examples"></a>Примеры определений JSON
Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные из источника HTTP в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемник. Это делается с помощью действия копирования в фабрике данных Azure.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Пример: копирование данных из источника HTTP в хранилище BLOB-объектов Azure
Решение фабрики данных для этого примера содержит следующие сущности фабрики данных.

1. Связанная служба типа [HTTP](#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [Http](#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [HttpSource](#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

В этом примере данные из источника HTTP каждый час копируются в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

### <a name="http-linked-service"></a>Связанная служба HTTP
В этом примере используется связанная служба HTTP с анонимной проверкой подлинности. Сведения о возможных типах аутентификации см. в разделе о [связанной службе HTTP](#linked-service-properties).

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure

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

### <a name="http-input-dataset"></a>Входной набор данных HTTP
Если для параметра **external** задать значение **true**, то фабрика данных воспримет этот набор данных как внешний, который создан не в результате какого-либо действия в фабрике данных.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Выходной набор данных BLOB-объекта Azure

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1).

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>Конвейер с действием копирования

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении конвейера JSON для типа **source** установлено значение **HttpSource**, а для типа **sink** — значение **BlobSink**.

Список свойств, поддерживаемых HttpSource, см. [здесь](#httpsource-in-copy-activity).

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
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

> [!NOTE]
> Сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

