---
title: "Копирование данных из Xero с помощью фабрики данных Azure (бета-версия) | Документация Майкрософт"
description: "Узнайте, как копировать данные из Xero в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure."
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
ms.openlocfilehash: aa81f9d163da8d9236470c0b797f5430163ed39d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Копирование данных из Xero с помощью фабрики данных Azure (бета-версия)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Xero. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data by using Copy Activity](v1/data-factory-data-movement-activities.md) (Перемещение данных с помощью действия копирования).

> [!IMPORTANT]
> Сейчас этот соединитель доступен в бета-версии. Попробуйте его и оставьте свой отзыв. Не используйте его в рабочих средах.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Xero можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Xero.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Xero поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Xero**. | Yes |
| host | Конечная точка сервера Xero (это api.xero.com).  | Yes |
| consumerKey | Ключ пользователя, связанный с приложением Xero. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в руководстве по [хранению учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| privateKey | Закрытый ключ из PEM-файла, созданный для частного приложения Xero. Включает весь текст из PEM-файла, в том числе окончания строк Unix (\n). Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в руководстве по [хранению учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Указывает, шифруются ли конечные точки источника данных с помощью протокола HTTPS. По умолчанию используется значение true.  | Нет  |
| useHostVerification | Указывает, нужно ли, чтобы имя узла в сертификате сервера совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет  |
| usePeerVerification | Указывает, следует ли проверять удостоверение сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет  |

**Пример.**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Xero.

Чтобы копировать данные из Xero, установите свойство type набора данных **XeroObject**. В этом типе набора данных нет дополнительных свойств для определенного типа.

**Пример**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Xero.

### <a name="xerosource-as-source"></a>XeroSource в качестве источника

Чтобы копировать данные из HTTP, установите тип источника **XeroSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **XeroSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM Contacts"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дополнительная информация
Список источников данных, которые поддерживает действие копирования, приведен в таблице [поддерживаемых хранилищ данных и форматов](copy-activity-overview.md#supported-data-stores-and-formats).
