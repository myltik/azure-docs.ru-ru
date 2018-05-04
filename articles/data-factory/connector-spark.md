---
title: Копирование данных из Spark с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Spark в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.date: 04/19/2018
ms.author: jingwang
ms.openlocfilehash: 4089fe636ad25f97fe78f0bd10553b93d768321d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Копирование данных из Spark с помощью фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Spark. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).


## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Spark можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю Spark.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Spark поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Spark** | Yes |
| host | IP-адрес или имя узла сервера Spark  | Yes |
| порт | TCP-порт, используемый сервером Spark для прослушивания клиентских подключений. При подключении к Azure HDInsights укажите порт 443. | Yes |
| serverType | Тип сервера Spark. <br/>Допустимые значения: **SharkServer**, **SharkServer2**, **SparkThriftServer**. | Нет  |
| thriftTransportProtocol | Транспортный протокол для использования в слое Thrift. <br/>Допустимые значения: **Binary**, **SASL**, **HTTP** | Нет  |
| authenticationType | Метод аутентификации, используемый для доступа к серверу Spark. <br/>Допустимые значения: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. | Yes |
| Имя пользователя | Имя пользователя, которое позволяет получить доступ к серверу Spark.  | Нет  |
| password | Пароль, соответствующий пользователю. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет  |
| httpPath | Частичный URL-адрес, соответствующий серверу Spark.  | Нет  |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL в локальных средах выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | Нет  |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowHostNameCNMismatch | Указывает, следует ли требовать, чтобы имя SSL-сертификата, выданного ЦС, совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. По умолчанию для этого параметра используется значение false.  | Нет  |
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
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Spark.

Чтобы скопировать данные с Spark, установите свойство type набора данных **SparkObject**. В этом типе набора данных нет дополнительных свойств для определенного типа.

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Spark.

### <a name="sparksource-as-source"></a>SparkSource в качестве источника

Чтобы скопировать данные из Spark, установите тип источника **SparkSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **SparkSource**. | Yes |
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

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
