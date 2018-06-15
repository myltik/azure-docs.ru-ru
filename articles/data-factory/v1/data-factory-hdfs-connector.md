---
title: Перемещение данных из локальной системы HDFS | Документация Майкрософт
description: Узнайте, как перемещать данные из локальной системы HDFS с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b8a35e19628ebbe75d3f1890a6d64ab777519698
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621553"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Перемещение данных из локальной системы HDFS с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](data-factory-hdfs-connector.md)
> * [Версия 2 — предварительная](../connector-hdfs.md)

> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, см. статью [Copy data from and to HDFS using Azure Data Factory](../connector-hdfs.md) (Копирование данных в и из HDFS с помощью фабрики данных Azure).

В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure перемещать данные из локальной системы HDFS. Этот документ является продолжением статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования.

Данные из HDFS можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Сейчас фабрика данных поддерживает перемещение данных из локальной системы HDFS в другие хранилища данных, но не наоборот.

> [!NOTE]
> Действие копирования не удаляет исходный файл после его успешного копирования в место назначения. Если необходимо удалить исходный файл после успешного копирования, создайте настраиваемое действие для удаления файла и используйте это действие в конвейере. 

## <a name="enabling-connectivity"></a>Включение соединения
Служба фабрики данных поддерживает подключение к локальной системе HDFS с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке. Шлюз используется для подключения к HDFS, даже если он размещен на виртуальных машинах Azure IaaS.

> [!NOTE]
> Убедитесь, что шлюзу управления данными доступны **ВСЕ** адреса [сервер_узла_имен]:[порт_узла_имен] и [сервер_узла_данных]:[порт_узла_данных] кластера Hadoop. По умолчанию используются [порт_узла_имен] 50070 и [порт_узла_данных] 50075.

Хотя шлюз можно установить на тот же локальный компьютер или виртуальную машину Azure, где находится HDFS, мы рекомендуем устанавливать шлюз на отдельный компьютер или отдельную виртуальную машину Azure IaaS. Если для шлюза будет выделен отдельный компьютер, это минимизирует вероятность конфликта ресурсов и повысит производительность. При установке шлюза на отдельный компьютер у компьютера должен быть доступ к машине с HDFS.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с действием копирования, которое перемещает данные из HDFS, с помощью разных средств и API-интерфейсов.

Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги:

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования.
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных.

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании инструментов и интерфейсов API (за исключением API .NET) вы самостоятельно определяете эти сущности фабрики данных в формате JSON.  Пример определений JSON для сущностей фабрики данных, которые используются для копирования данных из хранилища данных HDFS, вы найдете в разделе [Пример определений JSON. Копирование данных из локальной системы HDFS в хранилище BLOB-объектов Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) этой статьи.

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, характерных для системы HDFS.

## <a name="linked-service-properties"></a>Свойства связанной службы
Связанная служба привязывает хранилище данных к фабрике данных. Для связи локальной системы HDFS с фабрикой данных следует создать связанную службу типа **Hdfs**. В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе HDFS.

| Свойство | ОПИСАНИЕ | Обязательно |
| --- | --- | --- |
| Тип |Для свойства type необходимо задать значение **Hdfs** |Yes |
| URL-адрес |URL-адрес в HDFS |Yes |
| authenticationType |Anonymous или Windows. <br><br> Чтобы использовать **аутентификацию Kerberos** для соединителя HDFS, настройте локальную среду, как описано [здесь](#use-kerberos-authentication-for-hdfs-connector). |Yes |
| userName |Имя пользователя для проверки подлинности Windows. Для проверки подлинности Kerberos укажите `<username>@<domain>.com`. |Да (для проверки подлинности Windows) |
| password |Пароль для проверки подлинности Windows. |Да (для проверки подлинности Windows) |
| gatewayName |Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной системе HDFS. |Yes |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) выводит учетные данные доступа. |Нет  |

### <a name="using-anonymous-authentication"></a>Использовать анонимную проверку подлинности

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Использовать проверку подлинности Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Свойства набора данных
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных типа **FileShare** (который включает набор данных HDFS) содержит следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
| --- | --- | --- |
| folderPath |Путь к папке, Пример: `myfolder`<br/><br/>Чтобы указать специальные знаки в строке, используйте escape-знак "\". Например, для "папка\вложенная_папка" укажите "папка\\\\вложенная_папка", а для "d:\пример_папки" укажите "d:\\\\пример_папки".<br/><br/>Вы можете использовать это свойство вместе с параметром **partitionBy**, чтобы в путях к папкам учитывались дата и время начала и окончания среза. |Yes |
| fileName |Укажите имя файла в папке **folderPath** , если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: <br/><br/>Data<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.). |Нет  |
| partitionedBy |Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке (folderPath) каждый час будет другим. |Нет  |
| свойства | Поддерживаются следующие типы формата: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](data-factory-supported-file-and-compression-formats.md#text-format), [формате Json](data-factory-supported-file-and-compression-formats.md#json-format), [формате Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [формате Orc](data-factory-supported-file-and-compression-formats.md#orc-format) и [ формате Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет  |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. Узнайте больше о [форматах файлов и сжатия данных в фабрике данных Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Нет  |

> [!NOTE]
> Свойства filename и fileFilter нельзя использовать одновременно.

### <a name="using-partionedby-property"></a>Использование свойства partionedBy
Как сказано выше, для данных временных рядов путь к папке (folderPath) и имя файла (fileName) можно указывать динамически. Это делается с помощью свойства **partitionedBy**, [функций фабрики данных и системных переменных](data-factory-functions-variables.md).

Чтобы узнать больше о наборах данных временных рядов, планировании и срезах, ознакомьтесь со статьями [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md), [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) и [Конвейеры и действия в фабрике данных Azure: создание конвейеров, цепочки действий и расписаний для них](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Пример 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Пример 2

```JSON
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

В свою очередь свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Для действия копирования, когда источник относится к типу **FileSystemSource** , в разделе typeProperties доступны указанные ниже свойства.

**FileSystemSource** поддерживает следующие свойства:

| Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True, False (по умолчанию) |Нет  |

## <a name="supported-file-and-compression-formats"></a>Поддерживаемые форматы файлов и сжатия
Дополнительные сведения см. в статье [Форматы файлов и сжатия данных, поддерживаемые фабрикой данных Azure](data-factory-supported-file-and-compression-formats.md).

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Пример определений JSON. Копирование данных из локальной системы HDFS в хранилище BLOB-объектов Azure
Из этого примера вы узнаете, как копировать данные из локальной файловой системы HDFS в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Пример содержит определения JSON для следующих сущностей фабрики данных. Используя эти определения, вы можете создать конвейер для копирования данных из HDFS в хранилище BLOB-объектов Azure с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Связанная служба типа [OnPremisesHdfs](#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

В примере данные из локальной системы HDFS копируются в BLOB-объект Azure каждый час. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

**Связанная служба HDFS.** В этом примере используется проверка подлинности Windows. Сведения о различных типах аутентификации, которые можно использовать, см. в разделе [Свойства связанной службы HDFS](#linked-service-properties).

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
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

**Входной набор данных HDFS.** Этот набор данных ссылается на папку в файловой системе HDFS DataTransfer/UnitTest/. Конвейер копирует все файлы из этой папки в целевое расположение.

Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Действие копирования в конвейере с файловой системой в качестве источника и BLOB-объектом в качестве приемника**

Конвейер содержит действие копирования, которое использует эти входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **FileSystemSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **query** , выбирает для копирования данные за последний час.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Использование аутентификации Kerberos для соединителя HDFS
Есть два способа настроить локальную среду для использования аутентификации Kerberos в соединителе HDFS. Вы можете применить тот, который соответствует вашему сценарию.
* Способ 1. [Присоединение компьютера шлюза к области Kerberos](#kerberos-join-realm)
* Вариант 2. [Активация взаимного доверия между доменом Windows и областью Kerberos](#kerberos-mutual-trust).

### <a name="kerberos-join-realm"></a>Способ 1. Присоединение компьютера шлюза к области Kerberos

#### <a name="requirement"></a>Условие

* Компьютер шлюза нужно присоединить к области Kerberos; его нельзя присоединять к домену Windows.

#### <a name="how-to-configure"></a>Процесс настройки

**Настройка на компьютере шлюза**

1.  Запустите служебную программу **Ksetup** для настройки сервера центра распространения ключей и области Kerberos.

    Компьютер должен быть настроен как член рабочей группы, так как области Kerberos отличаются от доменов Windows. Для этого нужно настроить область Kerberos и добавить сервер центра распространения ключей, как описано ниже. Вместо *REALM.COM* укажите соответствующее имя области.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Перезапустите** компьютер после выполнения этих двух команд.

2.  Проверьте конфигурацию с помощью команды **Ksetup**. Результат должен выглядеть примерно так:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS на использование **аутентификации Windows**, указав имя участника Kerberos и пароль для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#linked-service-properties).

### <a name="kerberos-mutual-trust"></a>Вариант 2. Активация взаимного доверия между доменом Windows и областью Kerberos

#### <a name="requirement"></a>Условие
*   Компьютер шлюза должен быть присоединен к домену Windows.
*   Вам потребуются права на изменение параметров контроллера домена.

#### <a name="how-to-configure"></a>Процесс настройки

> [!NOTE]
> Вместо REALM.COM и AD.COM в приведенном ниже примере укажите соответствующие имена области и контроллера домена.

**Настройка на сервере центра распространения ключей**

1.  Измените конфигурацию центра распространения ключей в файле **krb5.conf**, чтобы создать отношения доверия с доменом Windows, как указано ниже в шаблоне конфигурации. По умолчанию эта конфигурация находится в файле **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Перезапустите** службу KDC после настройки.

2.  Подготовьте субъект-службу с именем **krbtgt/REALM.COM@AD.COM** на сервере центра распространения ключей, используя следующую команду:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  В файле **hadoop.security.auth_to_local** добавьте к конфигурации HDFS правило `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Настройка на контроллере домена**

1.  Выполните приведенные ниже команды **Ksetup**, чтобы добавить запись области:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Установите доверительные отношения между доменом Windows и областью Kerberos. Вместо [password] укажите пароль субъекта **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Выберите алгоритм шифрования, используемый в Kerberos.

    1. Последовательно выберите "Диспетчер серверов" > "Управление групповой политикой" > "Домен" > "Объекты групповой политики" > "Групповая политика по умолчанию или действующая групповая политика", затем нажмите "Редактировать".

    2. Откроется всплывающее окно **редактора управления групповой политикой**. Последовательно выберите "Конфигурация компьютера" > "Политики" > "Параметры Windows" > "Параметры безопасности" > "Локальные политики" > "Параметры безопасности". Затем щелкните **Сетевая безопасность: настройка типов шифрования, разрешенных Kerberos**.

    3. Выберите алгоритм шифрования, который вы хотите использовать при подключении к центру распространения ключей. Обычно можно просто выбрать все доступные параметры.

        ![Типы шифрования конфигурации для Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. С помощью команды **Ksetup** задайте алгоритм шифрования, который будет использоваться для этой области.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Создайте сопоставление между субъектом Kerberos и учетной записью домена, чтобы использовать субъект Kerberos в домене Windows.

    1. Щелкните "Средства администрирования" и выберите **Пользователи и компьютеры Active Directory**.

    2. Настройте расширенные функции, щелкнув **Просмотр** > **Расширенные функции**.

    3. Найдите учетную запись, для которой нужно создать сопоставление, и щелкните ее правой кнопкой мыши, чтобы открыть **Сопоставление имен** > **Имена Kerberos**.

    4. Добавьте субъект-службу из области.

        ![Сопоставление удостоверения безопасности](media/data-factory-hdfs-connector/map-security-identity.png)

**Настройка на компьютере шлюза**

* Выполните приведенные ниже команды **Ksetup**, чтобы добавить запись области.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS для использования **аутентификации Windows**, указав учетную запись домена или имя субъекта-службы Kerberos для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#linked-service-properties).

> [!NOTE]
> Сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.
