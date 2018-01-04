---
title: "Копирование данных из Spark, с помощью фабрики данных Azure | Документы Microsoft"
description: "Узнайте, как для копирования данных из Spark на поддерживаемых приемника хранилища данных с помощью операции копирования в конвейере фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: b422b3a721511a25b976586cd324d65f383ad140
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Копирование данных из Spark, с помощью фабрики данных Azure 

В этой статье описаны способы использования действия копирования в фабрике данных Azure для копирования данных из Spark. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).


## <a name="supported-capabilities"></a>Поддерживаемые возможности

Можно скопировать данные из Spark любой поддерживаемый приемник хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы предоставляют подробные сведения о свойствах, которые используются для определения объекты фабрики данных для соединителя Spark.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанных Spark службы поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **Spark** | Yes |
| host | IP-адрес или имя сервера Spark  | Yes |
| порт | TCP-порт, используемый сервером Spark на прослушивание клиентских соединений.  | Yes |
| serverType | Тип сервера Spark. <br/>Допустимые значения:: **SharkServer**, **SharkServer2**, **SparkThriftServer** | Нет  |
| thriftTransportProtocol | Транспортный протокол для использования в слое Thrift. <br/>Допустимые значения:: **двоичных**, **SASL**, ** HTTP ** | Нет  |
| authenticationType | Метод проверки подлинности, используемый для доступа к серверу Spark. <br/>Допустимые значения:: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Yes |
| Имя пользователя | Имя пользователя, который позволяет получить доступ к серверу Spark.  | Нет  |
| password | Пароль, соответствующий имени пользователя, указанный в поле имя пользователя, вы можете пометить это поле в виде строки SecureString, чтобы сохранить его безопасный в ADF, или сохранить пароль в хранилище ключей Azure и разрешить запросу действия копирования из него при выполнении копирования данных - Ост Дополнительные из RN [учетные данные хранятся в хранилище ключей](store-credentials-in-key-vault.md). | Нет  |
| httpPath | Частичное URL-адрес, соответствующий серверу Spark.  | Нет  |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| trustedCertPath | Полный путь PEM-файл, содержащий сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL на резидентных IR. Значение по умолчанию — файл cacerts.pem устанавливается вместе с IR.  | Нет  |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС в хранилище доверия системы или из указанного файла PEM. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowHostNameCNMismatch | Указывает, следует ли запрашивать имя сертификата SSL, выданный ЦС, чтобы отличаться от имени узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowSelfSignedServerCert | Указывает, следует ли разрешить самозаверяющие сертификаты с сервера. По умолчанию для этого параметра используется значение false.  | Нет  |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

**Пример.**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "httpPath" : "gateway/sandbox/spark"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел предоставляет набор свойств, поддерживаемых Spark набора данных.

Для копирования данных из Spark, установить свойство типа набора данных, **SparkObject**. Отсутствуют дополнительные свойства определенного типа в этот тип набора данных.

**Пример**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Spark.

### <a name="sparksource-as-source"></a>SparkSource в качестве источника

Для копирования данных из Spark, задайте тип источника в действии копирования для **SparkSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Источник действия копирования свойство типа должно быть присвоено: **SparkSource** | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
