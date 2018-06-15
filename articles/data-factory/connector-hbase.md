---
title: Копирование данных из HBase с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из HBase в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.date: 04/19/2018
ms.author: jingwang
ms.openlocfilehash: 0675c2d8a7f3a0a7929ff6f4b3e9a96c404a77c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616827"
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Копирование данных из HBase с помощью фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из HBase. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из HBase можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю HBase.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы HBase поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **HBase**. | Yes |
| host | IP-адрес или имя узла сервера HBase. (Например, 192.168.222.160, [имя_кластера].azurehdinsight.net.)  | Yes |
| порт | TCP-порт, используемый экземпляром HBase для прослушивания клиентских подключений. По умолчанию используется значение 9090. При подключении к Azure HDInsights укажите порт 443. | Нет  |
| httpPath | Частичный URL-адрес, соответствующий серверу HBase (например, /gateway/sandbox/hbase/version).  | Нет  |
| authenticationType | Механизм аутентификации, используемый для подключения к серверу HBase. <br/>Допустимые значения — **Anonymous** или **Basic**. | Yes |
| Имя пользователя | Имя пользователя, используемое для подключения к сущности HBase.  | Нет  |
| password | Пароль, соответствующий имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет  |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL в локальных средах выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | Нет  |
| allowHostNameCNMismatch | Указывает, следует ли требовать, чтобы имя SSL-сертификата, выданного ЦС, совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. По умолчанию для этого параметра используется значение false.  | Нет  |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

**Пример для HDInsights HBase:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<cluster name>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "<e.g. hbaserest>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример для универсальной среды HBase:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<host e.g. 192.168.222.160>",
            "port" : "<port>",
            "httpPath" : "<e.g. /gateway/sandbox/hbase/version>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true,
            "trustedCertPath" : "<trustedCertPath>",
            "allowHostNameCNMismatch" : true,
            "allowSelfSignedServerCert" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных HBase.

Чтобы скопировать данные из HBase, задайте для свойства type набора данных значение **HBaseObject**. В этом типе набора данных нет дополнительных свойств для определенного типа.

**Пример**

```json
{
    "name": "HBaseDataset",
    "properties": {
        "type": "HBaseObject",
        "linkedServiceName": {
            "referenceName": "<HBase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником HBase.

### <a name="hbasesource-as-source"></a>HBaseSource в качестве источника

Чтобы копировать данные из HBase, задайте для типа источника в действии копирования значение **HBaseSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **HBaseSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromHBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HBase input dataset name>",
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
                "type": "HBaseSource",
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
