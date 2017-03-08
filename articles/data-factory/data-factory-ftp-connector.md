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
ms.date: 01/24/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e7abf7b5cd6042ce64e6e09683b259bac82d6df
ms.openlocfilehash: 492c383865e86a19e56af816a77e02922adbc790


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Перемещение данных с FTP-сервера с использованием фабрики данных Azure
В этой статье описано, как с помощью действия копирования в фабрике данных Azure переместить данные с FTP-сервера в поддерживаемое хранилище данных-приемник. В этой статье в продолжение темы о [действиях перемещения данных](data-factory-data-movement-activities.md) приведены общие сведения о перемещении данных с помощью действия копирования, а также список поддерживаемых хранилищ данных, используемых в качестве источников и приемников.

Сейчас фабрика данных поддерживает только перемещение данных с FTP-сервера в другие хранилища данных, но не наоборот. Она поддерживает локальные и облачные FTP-серверы.

При перемещении данных из **локального** FTP-сервера в облачное хранилище данных (например, хранилище BLOB-объектов Azure) установите и используйте шлюз управления данными. Шлюз управления данными — это агент клиента, установленный на локальном компьютере, с помощью которого облачные службы подключаются к локальному ресурсу. Дополнительные сведения о шлюзе см. в статье [Шлюз управления данными](data-factory-data-management-gateway.md). Пошаговые инструкции по настройке и использованию шлюза см. в статье [moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Перемещение данных между локальными и облачными ресурсами). Шлюз используется для подключения к FTP-серверу, даже если сервер выполняется на виртуальной машине Azure IaaS.

Шлюз можно установить на тот же локальный компьютер или виртуальную машину Azure IaaS, что и FTP-сервер. Тем не менее рекомендуется установить шлюз на отдельный компьютер или виртуальную машину IaaS Azure, чтобы избежать конфликта ресурсов, а также для повышения производительности. При установке шлюза на отдельный компьютер у компьютера должен быть доступ к FTP-серверу.

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные с FTP-сервера — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Пример. Копирование данных с FTP-сервера в большой двоичный объект Azure
В этом примере показано, как скопировать данные с FTP-сервера в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

* Связанная служба типа [FtpServer](#ftp-linked-service-properties).
* Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
* Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](#fileshare-dataset-type-properties).
* Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](#ftp-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные с FTP-сервера каждый час копируются в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба FTP**. В этом примере приведена обычная аутентификация с использованием имени пользователя и пароля в виде обычного текста. Кроме того, можно использовать любой из следующих способов:

* анонимная аутентификация;
* обычная аутентификация и шифрование учетных данных;
* FTP через SSL или TLS (FTPS).

Сведения о возможных типах аутентификации см. в разделе [Связанная служба FTP](#ftp-linked-service-properties).

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
**Входной набор данных FTP**. Этот набор данных ссылается на папку `mysharedfolder` и файл `test.csv` FTP. Конвейер копирует файл в место назначения.

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


**Конвейер с действием копирования**

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

## <a name="ftp-linked-service-properties"></a>Свойства связанной службы FTP
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

Дополнительные сведения о настройке учетных данных для локального источника данных FTP см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Свойства типа "Действие копирования FTP"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования свойства типа различаются в зависимости от типов источников и приемников.

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

* [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .



<!--HONumber=Jan17_HO4-->


