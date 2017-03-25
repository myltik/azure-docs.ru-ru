---
title: "Перемещение данных из локальной системы HDFS | Документация Майкрософт"
description: "Узнайте, как перемещать данные из локальной системы HDFS с помощью фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8a6050fc52407ab6b974a9698d970248062665c1
ms.lasthandoff: 03/15/2017


---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Перемещение данных из локальной системы HDFS с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из локальной системы HDFS в другое хранилище данных. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Сейчас фабрика данных поддерживает перемещение данных из локальной системы HDFS в другие хранилища данных, но не наоборот.

## <a name="enabling-connectivity"></a>Включение соединения
Служба фабрики данных поддерживает подключение к локальной системе HDFS с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке. Шлюз используется для подключения к HDFS, даже если он размещен на виртуальных машинах Azure IaaS.

> [!NOTE]
> Убедитесь, что шлюзу управления данными доступны **ВСЕ** адреса [сервер_узла_имен]:[порт_узла_имен] и [сервер_узла_данных]:[порт_узла_данных] кластера Hadoop. По умолчанию используются [порт_узла_имен] 50070 и [порт_узла_данных] 50075.
>

Хотя шлюз можно установить на тот же локальный компьютер или виртуальную машину Azure, где находится HDFS, мы рекомендуем устанавливать шлюз на отдельный компьютер или отдельную виртуальную машину Azure IaaS. Если для шлюза будет выделен отдельный компьютер, это минимизирует вероятность конфликта ресурсов и повысит производительность. При установке шлюза на отдельный компьютер у компьютера должен быть доступ к машине с HDFS.

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из локальной системы HDFS, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные из локальной файловой системы HDFS в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Пример копирования данных из локальной системы HDFS в хранилище BLOB-объектов Azure
Из этого примера вы узнаете, как копировать данные из локальной файловой системы HDFS в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesHdfs](#hdfs-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](#hdfs-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](#hdfs-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из локальной системы HDFS копируются в BLOB-объект Azure каждый час. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

**Связанная служба HDFS**. В этом примере используется аутентификация Windows. Сведения о различных типах аутентификации, которые можно использовать, см. в разделе [Свойства связанной службы HDFS](#hdfs-linked-service-properties).

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

**Входной набор данных HDFS**. Этот набор данных ссылается на папку в файловой системе HDFS DataTransfer/UnitTest/. Конвейер копирует все файлы из этой папки в целевое расположение.

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

**Конвейер с действием копирования**

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

## <a name="hdfs-linked-service-properties"></a>Свойства связанной службы HDFS
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе HDFS.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **Hdfs** |Да |
| URL-адрес |URL-адрес в HDFS |Да |
| authenticationType |Anonymous или Windows. <br><br> Чтобы использовать **аутентификацию Kerberos** для соединителя HDFS, настройте локальную среду, как описано [здесь](#use-kerberos-authentication-for-hdfs-connector). |Да |
| userName |Имя пользователя для проверки подлинности Windows. |Да (для проверки подлинности Windows) |
| пароль |Пароль для проверки подлинности Windows. |Да (для проверки подлинности Windows) |
| gatewayName |Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной системе HDFS. |Да |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) выводит учетные данные доступа. |Нет |

Дополнительные сведения о настройке учетных данных для локальной системы HDFS см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

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
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Использование аутентификации Kerberos для соединителя HDFS
Есть два способа настроить локальную среду для использования аутентификации Kerberos в соединителе HDFS. Вы можете применить тот, который соответствует вашему сценарию.
* Вариант 1. [Включение компьютера шлюза в область Kerberos](#kerberos-join-realm).
* Вариант 2. [Активация взаимного доверия между доменом Windows и областью Kerberos](#kerberos-mutual-trust).

### <a name="kerberos-join-realm"></a>Вариант 1. Включение компьютера шлюза в область Kerberos

#### <a name="requirement"></a>Условие

* Компьютер шлюза нужно присоединить к области Kerberos; его нельзя присоединять к домену Windows.

#### <a name="how-to-configure"></a>Процесс настройки

**Настройка на компьютере шлюза**

1.    Запустите служебную программу **Ksetup** для настройки сервера центра распространения ключей и области Kerberos.

    Компьютер должен быть настроен как член рабочей группы, так как области Kerberos отличаются от доменов Windows. Для этого нужно настроить область Kerberos и добавить сервер центра распространения ключей, как описано ниже. Вместо *REALM.COM* укажите соответствующее имя области.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Перезапустите** компьютер после выполнения этих двух команд.

2.    Проверьте конфигурацию с помощью команды **Ksetup**. Должен быть получен следующий результат:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS на использование **аутентификации Windows**, указав имя участника Kerberos и пароль для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#hdfs-linked-service-properties).

### <a name="kerberos-mutual-trust"></a>Вариант 2. Активация взаимного доверия между доменом Windows и областью Kerberos

#### <a name="requirement"></a>Условие
*    Компьютер шлюза должен быть присоединен к домену Windows.
*    Вам потребуются права на изменение параметров контроллера домена.

#### <a name="how-to-configure"></a>Процесс настройки

> [!NOTE]
> Вместо REALM.COM и AD.COM в приведенном ниже примере укажите соответствующие имена области и контроллера домена.

**Настройка на сервере центра распространения ключей**

1.    Измените конфигурацию центра распространения ключей в файле **krb5.conf**, чтобы создать отношения доверия с доменом Windows, как указано ниже в шаблоне конфигурации. По умолчанию эта конфигурация находится в файле **/etc/krb5.conf**.

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

2.    Подготовьте субъект-службу с именем **krbtgt/REALM.COM@AD.COM** на сервере центра распространения ключей, используя следующую команду:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.    В файле **hadoop.security.auth_to_local** добавьте к конфигурации HDFS правило `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Настройка на контроллере домена**

1.    Запустите приведенную ниже команду **Ksetup**, чтобы добавить запись области:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.    Установите доверительные отношения между доменом Windows и областью Kerberos. Вместо [password] укажите пароль субъекта **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.    Выберите алгоритм шифрования, используемый в Kerberos.

    1. Последовательно выберите "Диспетчер серверов" > "Управление групповой политикой" > "Домен" > "Объекты групповой политики" > "Групповая политика по умолчанию или действующая групповая политика", затем нажмите "Редактировать".

    2. Откроется всплывающее окно **редактора управления групповой политикой**. Последовательно выберите "Конфигурация компьютера" > "Политики" > "Параметры Windows" > "Параметры безопасности" > "Локальные политики" > "Параметры безопасности". Затем щелкните **Сетевая безопасность: настройка типов шифрования, разрешенных Kerberos**.

    3. Выберите алгоритм шифрования, который вы хотите использовать при подключении к центру распространения ключей. Обычно можно просто выбрать все доступные параметры.

        ![Типы шифрования конфигурации для Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. С помощью команды **Ksetup** задайте алгоритм шифрования, который будет использоваться для этой области.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.    Создайте сопоставление между субъектом Kerberos и учетной записью домена, чтобы использовать субъект Kerberos в домене Windows.

    1. Щелкните "Средства администрирования" и выберите **Пользователи и компьютеры Active Directory**.

    2. Настройте расширенные функции, щелкнув **Просмотр** > **Расширенные функции**.

    3. Найдите учетную запись, для которой нужно создать сопоставление, и щелкните ее правой кнопкой мыши, чтобы открыть **Сопоставление имен** > **Имена Kerberos**.

    4. Добавьте субъект-службу из области.

        ![Сопоставление удостоверения безопасности](media/data-factory-hdfs-connector/map-security-identity.png)

**Настройка на компьютере шлюза**

* Запустите приведенную ниже команду **Ksetup**, чтобы добавить запись области:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS для использования **аутентификации Windows**, указав учетную запись домена или имя субъекта-службы Kerberos для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#hdfs-linked-service-properties).


## <a name="hdfs-dataset-type-properties"></a>Свойства типа "Набор данных HDFS"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных типа **FileShare** (который включает набор данных HDFS) содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| folderPath |Путь к папке, Пример: `myfolder`<br/><br/>Чтобы указать специальные знаки в строке, используйте escape-знак "\". Например, для "папка\вложенная_папка" укажите "папка\\\\вложенная_папка", а для "d:\пример_папки" укажите "d:\\\\пример_папки".<br/><br/>Вы можете использовать это свойство вместе с параметром **partitionBy**, чтобы в путях к папкам учитывались дата и время начала и окончания среза. |Да |
| fileName |Укажите имя файла в папке **folderPath** , если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: <br/><br/>Data<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.). |Нет |
| partitionedBy |Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке (folderPath) каждый час будет другим. |Нет |
| свойства | Поддерживаются следующие типы формата: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](#specifying-textformat), [формате Json](#specifying-jsonformat), [формате Avro](#specifying-avroformat), [формате Orc](#specifying-orcformat) и [ формате Parquet](#specifying-parquetformat). <br><br> Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. Дополнительные сведения см. в разделе [Указание сжатия](#specifying-compression). |Нет |

> [!NOTE]
> Свойства filename и fileFilter нельзя использовать одновременно.
>
>

### <a name="using-partionedby-property"></a>Использование свойства partionedBy
Как сказано выше, для временных рядов данных путь к папке и имя файла можно указывать динамически. Это делается с помощью свойства partitionedBy. Вы можете это сделать при помощи макроса фабрики данных и системных переменных SliceStart и SliceEnd, которые определяют логический период имеющегося среза данных.

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

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Свойства типа "Действие копирования HDFS"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Для действия копирования, когда источник относится к типу **FileSystemSource** , в разделе typeProperties доступны указанные ниже свойства.

**FileSystemSource** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True, False (по умолчанию) |Нет |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

