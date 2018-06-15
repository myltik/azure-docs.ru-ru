---
title: Копирование данных с SFTP-сервера с помощью фабрики данных Azure | Документация Майкрософт
description: Дополнительные сведения о соединителе MySQL в фабрике данных Azure, который позволяет скопировать данные с SFTP-сервера в хранилище данных, поддерживаемое в качестве приемника.
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
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 0cabb2f5618ef5179e2a25278fa2460556036d77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618418"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Копирование данных с SFTP-сервера с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-sftp-connector.md)
> * [Версия 2 — предварительная](connector-sftp.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные с SFTP-сервера. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных с SFTP-сервера с помощью фабрики данных Azure](v1/data-factory-sftp-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные с SFTP-сервера можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SFTP поддерживает:

- Копирование файлов с использованием **базовой** проверки подлинности или проверки подлинности **SshPublicKey**.
- Копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к SFTP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SFTP поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Sftp**. |Yes |
| host | Имя или IP-адрес SFTP-сервера. |Yes |
| порт | Порт, прослушиваемый SFTP-сервером.<br/>Допустимые значения: целые числа. Значение по умолчанию: **22**. |Нет  |
| skipHostKeyValidation | Указывает, нужно ли пропустить проверку ключа узла.<br/>Допустимые значения: **true**, **false** (по умолчанию).  | Нет  |
| hostKeyFingerprint | Содержит отпечаток ключа узла. | Да, если skipHostKeyValidation имеет значение false.  |
| authenticationType | Укажите тип аутентификации.<br/>Допустимые значения: **Базовый**, **SshPublicKey**. Описание свойств и примеры JSON для каждого типа см. ниже в разделах [использование обычной аутентификации](#using-basic-authentication) и [использование аутентификации с открытым ключом SSH](#using-ssh-public-key-authentication) соответственно. |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

### <a name="using-basic-authentication"></a>Использование обычной аутентификации

Чтобы использовать базовую проверку подлинности, задайте значение **Базовый**, а также все следующие свойства в дополнение к универсальным свойствам соединителя SFTP, которые описаны в последнем разделе.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| userName | Пользователь, имеющий доступ к SFTP-серверу. |Yes |
| password | Пароль пользователя (userName). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Пример.**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>Использование аутентификации с открытым ключом SSH

Чтобы использовать проверку подлинности с открытым ключом SSH, задайте для свойства authenticationType значение **SshPublicKey**, а также все следующие свойства в дополнение к универсальным свойствам соединителя SFTP, которые описаны в последнем разделе.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| userName | Пользователь, имеющий доступ к SFTP-серверу. |Yes |
| privateKeyPath | Укажите доступный для среды выполнения интеграции абсолютный путь к файлу закрытого ключа. Применяется, только если в connectVia задан тип среды выполнения интеграции. | Должно быть указано одно из свойств: `privateKeyPath` или `privateKeyContent`.  |
| privateKeyContent | Содержимое закрытого ключа SSH в кодировке Base64. Закрытый ключ SSH должен быть в формате OpenSSH. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Должно быть указано одно из свойств: `privateKeyPath` или `privateKeyContent`. |
| passPhrase | Укажите пароль или парольную фразу для расшифровки закрытого ключа, если они используются для защиты файлы ключа. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да, если файл закрытого ключа защищен парольной фразой. |

> [!NOTE]
> Соединитель SFTP поддерживает только ключ OpenSSH RSA/DSA. Содержимое файла ключа должно начинаться с текста "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Если файл закрытого ключа имеет формат PPK, используйте средство Putty для преобразования из формата PPK в OpenSSH. 

**Пример 1. Проверка подлинности с закрытым ключом SSH, для которого указан путь к файлу**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2. Проверка подлинности с закрытым ключом SSH, для которого указано содержимое**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных SFTP.

Чтобы скопировать данные с FTP, установите свойство type набора данных **FileShare**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **FileShare**. |Yes |
| folderPath | Путь к папке, Фильтр подстановочных знаков не поддерживается. Например: папка/вложенная папка/ |Yes |
| fileName |  **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые подстановочные знаки для фильтра: `*` (несколько знаков) и `?` (один знак).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ. |Нет  |
| свойства | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет  |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

>[!NOTE]
>Если вы использовали свойство fileFilter для фильтрации файлов, оно по-прежнему поддерживается без изменений, а вам предлагается далее использовать новую возможность фильтрации, добавленную к fileName.

**Пример.**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SFTP.

### <a name="sftp-as-source"></a>SFTP в качестве источника

Чтобы копировать данные из SFTP, установите тип источника **FileSystemSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **FileSystemSource**. |Yes |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет  |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
