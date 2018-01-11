---
title: "Копирование данных из Oracle Eloqua, с помощью фабрики данных Azure (бета-версия) | Документы Microsoft"
description: "Узнайте, как для копирования данных из Oracle Eloqua на поддерживаемых приемника хранилища данных с помощью операции копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: 348b1a6ed8f8acd32b17e79b389584e08c394b5b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-beta"></a>Копирование данных из Oracle Eloqua, с помощью фабрики данных Azure (бета-версия)

В этой статье описаны способы использования действия копирования в фабрике данных Azure для копирования данных из Oracle Eloqua. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

> [!IMPORTANT]
> Этот соединитель в настоящее время находится в бета-версии. Можно испытать его и отправить отзыв. Не используйте его в производственных средах.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Можно скопировать данные из Oracle Eloqua любой поддерживаемый приемник хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы предоставляют подробные сведения о свойствах, которые используются для определения объекты фабрики данных для Oracle Eloqua соединителя.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для Oracle Eloqua связанные службы поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Свойство типа должно быть присвоено: **Eloqua** | Yes |
| endpoint | Конечная точка сервера Eloqua. (то есть, eloqua.example.com)  | Yes |
| Имя пользователя | Имя узла и имя пользователя учетной записи Eloqua в форме: узел имя или имя пользователя. (то есть Eloqua/Анна)  | Yes |
| password | Пароль, соответствующий имени пользователя. Можно выбрать в этом поле в виде строки SecureString, сохраните его в безопасном месте в ADF или сохранить пароль в хранилище ключей Azure и позволить действие копирования по запросу из него при выполнении копирования данных — ознакомьтесь с дополнительными [учетные данные хранятся в хранилище ключей](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Указывает, шифруются ли конечные точки источника данных с помощью протокола HTTPS. По умолчанию используется значение true.  | Нет  |
| useHostVerification | Указывает, следует ли запрашивать имя узла в сертификате сервера должно соответствовать имени узла сервера, при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет  |
| usePeerVerification | Указывает, следует ли проверять подлинность сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет  |

**Пример.**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "eloqua.example.com",
            "username" : "Eloqua/Alice",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел предоставляет набор свойств, поддерживаемые Oracle Eloqua набора данных.

Для копирования данных из Oracle Eloqua, установить свойство типа набора данных, **EloquaObject**. Отсутствуют дополнительные свойства определенного типа в этот тип набора данных.

**Пример**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Oracle Eloqua.

### <a name="eloquasource-as-source"></a>EloquaSource в качестве источника

Для копирования данных из Oracle Eloqua, задайте тип источника в действии копирования для **EloquaSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Источник действия копирования свойство типа должно быть присвоено: **EloquaSource** | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM Accounts"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
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
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
Список поддерживаемых данным фабрики данных Azure см. в разделе [поддерживается хранилищ данных](copy-activity-overview.md#supported-data-stores-and-formats).
