---
title: "Перемещение данных с FTP-сервера с использованием фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как перемещать данные с FTP-сервера с использованием фабрики данных Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 3f859aed911b04353273d1faa5ead8d4c48c2a05
ms.lasthandoff: 03/27/2017


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Перемещение данных с FTP-сервера с использованием фабрики данных Azure
В этой статье описывается, как с помощью действия копирования в фабрике данных Azure перемещать данные с FTP-сервера. Это продолжение статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования.

Данные с FTP-сервера можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Сейчас фабрика данных поддерживает только перемещение данных с FTP-сервера в другие хранилища данных, но не наоборот. Она поддерживает локальные и облачные FTP-серверы.

## <a name="enabling-connectivity"></a>Включение соединения
При перемещении данных из **локального** FTP-сервера в облачное хранилище данных (например, хранилище BLOB-объектов Azure) установите и используйте шлюз управления данными. Шлюз управления данными — это агент клиента, установленный на локальном компьютере, с помощью которого облачные службы подключаются к локальному ресурсу. Дополнительные сведения о шлюзе см. в статье [Шлюз управления данными](data-factory-data-management-gateway.md). Пошаговые инструкции по настройке и использованию шлюза см. в статье [moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Перемещение данных между локальными и облачными ресурсами). Шлюз используется для подключения к FTP-серверу, даже если сервер выполняется на виртуальной машине Azure IaaS.

Шлюз можно установить на тот же локальный компьютер или виртуальную машину Azure IaaS, что и FTP-сервер. Тем не менее рекомендуется установить шлюз на отдельный компьютер или виртуальную машину IaaS Azure, чтобы избежать конфликта ресурсов, а также для повышения производительности. При установке шлюза на отдельный компьютер у компьютера должен быть доступ к FTP-серверу.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с действием копирования, который перемещает данные с исходного FTP-сервера, с помощью разных инструментов и интерфейсов API.

Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Независимо от того, используются инструменты или интерфейсы API, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги. 

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования. 
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных. 

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании инструментов и интерфейсов API (за исключением API .NET) вы самостоятельно определяете эти сущности фабрики данных в формате JSON.  Пример определений JSON для сущностей фабрики данных, которые используются для копирования данных из хранилища данных FTP, доступен в разделе [Пример JSON. Копирование данных с FTP-сервера в большой двоичный объект Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) этой статьи. 

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к FTP.

## <a name="linked-service-properties"></a>Свойства связанной службы
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе FTP.

| Свойство | Описание | Обязательно | значение по умолчанию |
| --- | --- | --- | --- |
| type |Для свойства type необходимо задать значение FtpServer |Да |&nbsp; |
| host |Имя или IP-адрес FTP-сервера |Да |&nbsp; |
| authenticationType |Укажите тип аутентификации |Да |Обычная, анонимная |
| Имя пользователя |Пользователь, имеющий доступ к FTP-серверу |Нет |&nbsp; |
| password |Пароль для пользователя (имя пользователя) |Нет |&nbsp; |
| encryptedCredential |Зашифрованные учетные данные для доступа к FTP-серверу |Нет |&nbsp; |
| gatewayName |Имя шлюза управления данными для подключения к локальному FTP-серверу |Нет |&nbsp; |
| порт |Порт, прослушиваемый FTP-сервером |Нет |21 |
| enableSsl |Укажите, какой канал следует использовать (FTP через SSL или TLS) |Нет |Да |
| enableServerCertificateValidation |Укажите, следует ли включать проверку SSL-сертификата на сервере при использовании канала FTP через SSL или TLS |Нет |Да |

### <a name="using-anonymous-authentication"></a>Использовать анонимную проверку подлинности

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Использование имени пользователя и пароля в виде обычного текста для обычной аутентификации

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Использование свойств port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Использование свойства encryptedCredential для аутентификации и шлюза

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>Свойства набора данных
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных.

Разделы **typeProperties** для каждого типа набора данных отличаются. Он предоставляет сведения, которые относятся к типу набора данных. Раздел typeProperties набора данных с типом **FileShare** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| folderPath |Подпуть к папке. Чтобы указать специальные знаки в строке, используйте escape-знак "\". Примеры приведены в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions).<br/><br/>Вы можете использовать это свойство вместе с параметром **partitionBy**, чтобы в путях к папкам учитывались дата и время начала и окончания среза. |Да |
| fileName |Укажите имя файла в папке **folderPath** , если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: <br/><br/>Data<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Нет |
| fileFilter |Укажите фильтр для выбора подмножества файлов из folderPath. Фильтр дает возможность выбирать только некоторые файлы, а не все.<br/><br/>Допустимые значения: `*` (несколько знаков) и `?` (один знак).<br/><br/>Пример 1: `"fileFilter": "*.log"`<br/>Пример 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> Свойство fileFilter применяется к входному набору данных FileShare. HDFS не поддерживает это свойство. |Нет |
| partitionedBy |Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке folderPath каждый час будет другим. |Нет |
| свойства | Поддерживаются следующие типы формата: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](data-factory-supported-file-and-compression-formats.md#text-format), [формате Json](data-factory-supported-file-and-compression-formats.md#json-format), [формате Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [формате Orc](data-factory-supported-file-and-compression-formats.md#orc-format) и [ формате Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. Узнайте больше о [форматах файлов и сжатия данных в фабрике данных Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Нет |
| useBinaryTransfer |Укажите, использовать ли режим передачи в двоичном формате. Значение true, если использовать двоичный режим, и false, если ASCII. Значение по умолчанию: True. Это свойство можно использовать, только когда тип связанной службы является FTP-сервер. |Нет |

> [!NOTE]
> Свойства filename и fileFilter нельзя использовать одновременно.

### <a name="using-partionedby-property"></a>Использование свойства partionedBy
Как сказано выше, для данных временных рядов путь к папке (folderPath) и имя файла (fileName) можно указывать динамически. Это можно сделать с помощью свойства **partitionedBy**, [функций фабрики данных и системных переменных](data-factory-functions-variables.md).

Чтобы узнать о наборах данных временных рядов, планировании и срезах, ознакомьтесь со статьями [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md), [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) и [Конвейеры и действия в фабрике данных Azure](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Пример 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например, wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Пример 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
В этом примере год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.



## <a name="copy-activity-properties"></a>Свойства действия копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования свойства типа различаются в зависимости от типов источников и приемников.

Если источник относится к типу **FileSystemSource**, в действии копирования, в разделе typeProperties доступны указанные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True, False (по умолчанию) |Нет |


## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Пример JSON. Копирование данных с FTP-сервера в большой двоичный объект Azure
В этом примере показано, как скопировать данные с FTP-сервера в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

* Связанная служба типа [FtpServer](#linked-service-properties).
* Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](#dataset-properties).
* Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

В этом примере данные с FTP-сервера каждый час копируются в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба FTP**. В этом примере приведена обычная аутентификация с использованием имени пользователя и пароля в виде обычного текста. Кроме того, можно использовать любой из следующих способов:

* анонимная аутентификация;
* обычная аутентификация и шифрование учетных данных;
* FTP через SSL или TLS (FTPS).

Сведения о возможных типах аутентификации см. в разделе [Связанная служба FTP](#linked-service-properties).

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
**Связанная служба хранилища Azure**

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
**Входной набор данных FTP**. Этот набор данных ссылается на папку `mysharedfolder` и файл `test.csv` на FTP-сервере. Конвейер копирует файл в место назначения.

Если параметру external присвоить значение true, то фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой фабрике данных.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
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


**Действие копирования в конвейере с файловой системой в качестве источника и большим двоичным объектом в качестве приемника**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **FileSystemSource**, а для типа **sink** — значение **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в разделе [Сопоставление столбцов исходного набора данных со столбцами целевого набора данных](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

* [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

