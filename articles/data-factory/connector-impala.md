---
title: Копирование данных из Impala с помощью фабрики данных Azure (бета-версия) | Документация Майкрософт
description: Узнайте, как копировать данные из Impala в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 73987d03cb96fa421d193504fe6eaf6c3b5ddb18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618775"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>Копирование данных из Impala с помощью фабрики данных Azure (бета-версия)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Impala. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется фабрика данных версии 1, которая является общедоступной, ознакомьтесь со статьей [Перемещение данных с помощью действия копирования](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Сейчас этот соединитель доступен в бета-версии. Попробуйте его и оставьте свой отзыв. Не используйте его в рабочих средах.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Impala можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

 В фабрике данных предоставляется встроенный драйвер, который обеспечивает подключение. Поэтому не нужно вручную устанавливать драйвер для использования этого соединителя.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Impala.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Impala поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Impala**. | Yes |
| host | IP-адрес или имя узла сервера Impala (192.168.222.160).  | Yes |
| порт | TCP-порт, используемый сервером Impala для прослушивания клиентских подключений. Значение по умолчанию — 21050.  | Нет  |
| authenticationType | Тип проверки подлинности. <br/>Допустимые значения: **Anonymous**, **SASLUsername** и **UsernameAndPassword**. | Yes |
| Имя пользователя | Имя пользователя, используемое для доступа к серверу Impala. Значение по умолчанию является анонимным при использовании SASLUsername.  | Нет  |
| password | Пароль, который соответствует имени пользователя при использовании UsernameAndPassword. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет  |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. Значение по умолчанию — **false**.  | Нет  |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС, используемые для проверки сервера при подключении по протоколу SSL. Это свойство можно задать, только если SSL используется в локальной среде выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе со средой выполнения интеграции.  | Нет  |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. Значение по умолчанию — **false**.  | Нет  |
| allowHostNameCNMismatch | Указывает, следует ли требовать, чтобы имя SSL-сертификата, выданного ЦС, совпадало с именем узла сервера при подключении по протоколу SSL. Значение по умолчанию — **false**.  | Нет  |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. Значение по умолчанию — **false**.  | Нет  |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

**Пример.**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Impala.

Чтобы скопировать данные из Impala, установите свойство type набора данных **ImpalaObject**. В этом типе набора данных нет дополнительных свойств для определенного типа.

**Пример**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых типом источника Impala.

### <a name="impala-as-a-source-type"></a>Impala в качестве источника данных

Чтобы копировать данные из Impala, установите тип источника **ImpalaSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type источника действия копирования необходимо задать значение **ImpalaSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
