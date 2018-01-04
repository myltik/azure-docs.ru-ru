---
title: "Скопировать данные из Google BigQuery, с помощью фабрики данных Azure (бета-версия) | Документы Microsoft"
description: "Узнайте, как скопировать данные из Google BigQuery на поддерживаемых приемника хранилища данных с помощью операции копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: 00962b1bb32ff096712d36c07620505e72667380
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-beta"></a>Копирование данных из Google BigQuery, с помощью фабрики данных Azure (бета-версия)

В этой статье описаны способы использования действия копирования в фабрике данных Azure для копирования данных из Google BigQuery. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

> [!IMPORTANT]
> Этот соединитель в настоящее время находится в бета-версии. Можно проверить их работу и оставить отзыв. Не используйте его в производственных средах.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Можно скопировать данные из Google BigQuery любой поддерживаемый приемник хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы предоставляют подробные сведения о свойствах, которые используются для определения соединитель Google BigQuery объекты фабрики данных.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для Google BigQuery связанная служба поддерживает следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **GoogleBigQuery** | Yes |
| Проект | Проект BigQuery по умолчанию для запросов к.  | Yes |
| additionalProjects | Список разделенных запятыми public BigQuery проектов для доступа.  | Нет  |
| requestGoogleDriveScope | Следует ли запрашивать доступ к Google диске. Разрешение доступа Google диске включает поддержку для федеративных таблиц, объединяющие данные BigQuery с данными с Google диска. По умолчанию для этого параметра используется значение false.  | Нет  |
| authenticationType | Механизм проверки подлинности OAuth 2.0, используемый для проверки подлинности. ServiceAuthentication может использоваться только на резидентных IR. <br/>Допустимые значения:: **ServiceAuthentication**, **UserAuthentication** | Yes |
| RefreshToken | Токен обновления, полученный из Google для авторизации доступа к BigQuery для UserAuthentication. Можно выбрать в этом поле в виде строки SecureString, сохраните его в безопасном месте в ADF или сохранить пароль в хранилище ключей Azure и позволить действие копирования по запросу из него при выполнении копирования данных — ознакомьтесь с дополнительными [учетные данные хранятся в хранилище ключей](store-credentials-in-key-vault.md). | Нет  |
| email | Идентификатор электронной почты учетной записи службы, используемый для ServiceAuthentication и может использоваться только на резидентных IR.  | Нет  |
| keyFilePath | Полный путь к файлу ключа .p12, используемый для проверки подлинности адрес электронной почты учетной записи службы и может использоваться только на резидентных IR.  | Нет  |
| trustedCertPath | Полный путь PEM-файл, содержащий сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL на резидентных IR. Значение по умолчанию — файл cacerts.pem устанавливается вместе с IR.  | Нет  |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС в хранилище доверия системы или из указанного файла PEM. По умолчанию для этого параметра используется значение false.  | Нет  |

**Пример.**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project>",
            "additionalProjects" : "<additionalProjects>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел предоставляет набор свойств, поддерживаемых Google BigQuery набора данных.

Чтобы скопировать данные из Google BigQuery, установить свойство типа набора данных, **GoogleBigQueryObject**. Отсутствуют дополнительные свойства определенного типа в этот тип набора данных.

**Пример**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Google BigQuery.

### <a name="googlebigquerysource-as-source"></a>GoogleBigQuerySource в качестве источника

Чтобы скопировать данные из Google BigQuery, задайте тип источника в действии копирования для **GoogleBigQuerySource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Источник действия копирования свойство типа должно быть присвоено: **GoogleBigQuerySource** | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
