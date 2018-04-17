---
title: Копирование данных из источника HTTP с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из облака или локального источника HTTP на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 3aca66d6922273e78b5100948f1b868c6c9b56af
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Копирование данных из источника HTTP с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-http-connector.md)
> * [Версия 2 — предварительная](connector-http.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из конечной точки HTTP. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Перемещение данных из источника HTTP с помощью фабрики данных Azure](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из источника HTTP можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель HTTP поддерживает:

- Извлечение данных из конечной точки HTTP с помощью метода **GET** или **POST** HTTP.
- Получение данных с помощью следующих проверок подлинности: **Anonymous**, **Basic**, **Digest**, **Windows** и **ClientCertificate**.
- Копирование ответа HTTP "как есть" или его анализ с использованием [поддерживаемых форматов файлов и сжатия кодеков](supported-file-formats-and-compression-codecs.md).

Разница между этим соединителем и [соединителем для веб-таблиц](connector-web-table.md) заключается в том, что соединитель для веб-таблиц используется для извлечения содержимого таблицы из веб-страницы HTML.

>[!TIP]
>Для проверки HTTP-запроса на получение данных перед настройкой соединителя HTTP в ADF ознакомьтесь с требованиями к оформлению заголовка и текста в спецификации API и используйте такие средства, как Postman или веб-браузер.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю HTTP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы HTTP поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **HttpServer**. | Yes |
| URL-адрес | Базовый URL-адрес веб-сервера. | Yes |
| enableServerCertificateValidation | Укажите, следует ли включать проверку SSL-сертификата на сервере при подключении к конечной точке HTTP. | Нет. Значение по умолчанию — true. |
| authenticationType | Указывает тип проверки подлинности. Допустимые значения: **Anonymous**, **Basic**, **Digest**, **Windows** и **ClientCertificate**. <br><br> В разделах ниже описываются дополнительные свойства и приведены примеры JSON для поддерживаемых типов проверки подлинности. | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

### <a name="using-basic-digest-or-windows-authentication"></a>Использование типов проверки подлинности Basic, Digest или Windows

Задайте для свойства authenticationType значение **Basic**, **Digest** или **Windows** и укажите следующие свойства вместе с универсальными свойствами, описанными в предыдущем разделе:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| userName | Имя пользователя для доступа к конечной точке HTTP. | Yes |
| password | Пароль пользователя (userName). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Пример**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>Использование типа проверки подлинности ClientCertificate

Чтобы использовать проверку подлинности ClientCertificate, установите для свойства authenticationType значение **ClientCertificate** и укажите следующие свойства вместе с универсальными свойствами, описанными в предыдущем разделе:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| embeddedCertData | Данные сертификата в кодировке Base64. | Должно быть указано одно из свойств: `embeddedCertData` или `certThumbprint`. |
| certThumbprint | Отпечаток сертификата, установленного в хранилище сертификатов компьютера локальной среды выполнения интеграции. Применяется, только если в connectVia задан тип локальной среды выполнения интеграции. | Должно быть указано одно из свойств: `embeddedCertData` или `certThumbprint`. |
| password | Пароль, связанный с сертификатом. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет  |

Если вы используете свойство certThumbprint для проверки подлинности, а сертификат установлен в личном хранилище локального компьютера, вам необходимо предоставить разрешение на чтение для локальной среды выполнения интеграции.

1. Запустите консоль управления (MMC). Добавьте оснастку **Сертификаты**, которая связана с **локальным компьютером**.
2. Разверните **Сертификаты**, **Личные**, а затем щелкните **Сертификаты**.
3. Щелкните правой кнопкой мыши сертификат в личном хранилище, а затем выберите **Все задачи** -> **Управление закрытыми ключами...**
3. На вкладке **Безопасность** добавьте учетную запись пользователя, с помощью которой запущена служба узла локальной среды выполнения интеграции (DIAHostService) с доступом на чтение к сертификату.

**Пример 1. Использование certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2. Использование embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных HTTP.

Чтобы скопировать данные из HTTP, установите свойство типа набора данных **HttpFile**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **HttpFile**. | Yes |
| relativeUrl | Относительный URL-адрес ресурса, который содержит данные. Если свойство не задано, используется только URL-адрес, указанный в определении связанной службы. | Нет  |
| requestMethod | Метод HTTP.<br/>Допустимые значения: **GET** (по умолчанию) или **POST**. | Нет  |
| additionalHeaders | Дополнительные заголовки HTTP-запроса. | Нет  |
| requestBody | Текст HTTP-запроса. | Нет  |
| свойства | Если требуется **получить данные из конечной точки HTTP "как есть"**, — без анализа и копирования в хранилище файлов — можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Поддерживаемые форматы для выполнения анализа содержимого ответа HTTP в процессе выполнения операции копирования: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет  |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет  |

**Пример 1. Использование метода Get (по умолчанию)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Пример 2. Использование метода Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником HTTP.

### <a name="http-as-source"></a>HTTP в качестве источника

Чтобы копировать данные из HTTP, установите тип источника **HttpSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **HttpSource**. | Yes |
| httpRequestTimeout | Время ожидания ответа для HTTP-запроса. Это интервал времени для получения ответа, а не считывания данных ответа.<br/> Значение по умолчанию — 00:01:40  | Нет  |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.