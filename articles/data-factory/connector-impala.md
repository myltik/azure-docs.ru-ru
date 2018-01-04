---
title: "Копирование данных из Impala Apache, с помощью фабрики данных Azure (бета-версия) | Документы Microsoft"
description: "Узнайте, как скопировать данные из Apache Impala на поддерживаемых приемника хранилища данных с помощью операции копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: 4766e19b1823bdb737be8a90b3e2e2bfe4e48ab9
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-apache-impala-using-azure-data-factory-beta"></a>Копирование данных из Impala Apache, с помощью фабрики данных Azure (бета-версия)

В этой статье описаны способы использования действия копирования в фабрике данных Azure для копирования данных из Apache Impala. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

> [!IMPORTANT]
> Этот соединитель в настоящее время находится в бета-версии. Можно испытать его и отправить отзыв. Не используйте его в производственных средах.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Можно скопировать данные из Apache Impala любой поддерживаемый приемник хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы предоставляют подробные сведения о свойствах, которые используются для определения объекты фабрики данных Apache Impala соединитель.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для Apache Impala связанная служба поддерживает следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **Apache Impala** | Yes |
| host | IP-адрес или имя сервера Apache Impala. (это 192.168.222.160)  | Yes |
| порт | TCP-порт, используемый сервером Apache Impala на прослушивание клиентских соединений. Значение по умолчанию — 21050.  | Нет  |
| authenticationType | Тип проверки подлинности для использования. <br/>Допустимые значения:: **Anonymous**, **SASLUsername**, **UsernameAndPassword** | Yes |
| Имя пользователя | Имя пользователя, используемое для доступа к серверу Apache Impala. Значение по умолчанию является анонимным, при использовании SASLUsername.  | Нет  |
| password | Пароль, соответствующий имени пользователя при использовании UsernameAndPassword. Можно выбрать в этом поле в виде строки SecureString, сохраните его в безопасном месте в ADF или сохранить пароль в хранилище ключей Azure и позволить действие копирования по запросу из него при выполнении копирования данных — ознакомьтесь с дополнительными [учетные данные хранятся в хранилище ключей](store-credentials-in-key-vault.md). | Нет  |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| trustedCertPath | Полный путь PEM-файл, содержащий сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL на резидентных IR. Значение по умолчанию — файл cacerts.pem устанавливается вместе с IR.  | Нет  |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС в хранилище доверия системы или из указанного файла PEM. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowHostNameCNMismatch | Указывает, следует ли запрашивать имя сертификата SSL, выданный ЦС, чтобы отличаться от имени узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет  |
| allowSelfSignedServerCert | Указывает, следует ли разрешить самозаверяющие сертификаты с сервера. По умолчанию для этого параметра используется значение false.  | Нет  |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

**Пример.**

```json
{
    "name": "Apache ImpalaLinkedService",
    "properties": {
        "type": "Apache Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел предоставляет набор свойств, поддерживаемых Apache Impala набора данных.

Чтобы скопировать данные из Apache Impala, установить свойство типа набора данных, **Apache ImpalaObject**. Отсутствуют дополнительные свойства определенного типа в этот тип набора данных.

**Пример**

```json
{
    "name": "Apache ImpalaDataset",
    "properties": {
        "type": "Apache ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Apache Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Apache Impala.

### <a name="apache-impalasource-as-source"></a>Apache ImpalaSource в качестве источника

Чтобы скопировать данные из Apache Impala, задайте тип источника в действии копирования для **Apache ImpalaSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Источник действия копирования свойство типа должно быть присвоено: **Apache ImpalaSource** | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromApache Impala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Apache Impala input dataset name>",
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
                "type": "Apache ImpalaSource",
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
Список данных хранилища данных, поддерживаемые в фабрике данных Azure, см. в разделе [поддерживается хранилищ данных](copy-activity-overview.md#supported-data-stores-and-formats).
