---
title: "Хранение учетных данных в Azure Key Vault | Документация Майкрософт"
description: "Узнайте, как хранить учетные данные для хранилищ данных, используемых в Azure Key Vault, которые фабрика данных Azure может автоматически извлекать в среду выполнения."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: f7604e251bd62ec382ac9ace3de058e345abb863
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Хранение учетных данных в Azure Key Vault

Учетные данные для хранилищ данных можно хранить в [Azure Key Vault](../key-vault/key-vault-whatis.md). Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных. В настоящее время только [соединитель Dynamics](connector-dynamics-crm-office-365.md) и [соединитель Salesforce](connector-salesforce.md) поддерживает эту функцию.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Предварительные требования

Эта функция основывается на удостоверении службы фабрики данных. Узнайте, как работает [удостоверение службы фабрики данных](data-factory-service-identity.md), и убедитесь, что фабрике данных назначено удостоверение.

## <a name="steps"></a>Действия

Чтобы указать учетные данные, хранимые в Azure Key Vault, необходимо:

1. [Получите удостоверение службы фабрики данных](data-factory-service-identity.md#retrieve-service-identity). Для этого скопируйте значение "SERVICE IDENTITY APPLICATION ID" (Идентификатор приложения удостоверения службы), созданное вместе с фабрикой.
2. Предоставьте удостоверению службы доступ к Azure Key Vault. Выберите элементы "Хранилище ключей -> Управление доступом -> Добавить". Найдите идентификатор приложения удостоверения службы, чтобы добавить разрешения на **чтение**. Это позволит указанной фабрике получить доступ к секрету в хранилище ключей.
3. Создайте связанную службу, указывающую на Azure Key Vault. Дополнительные сведения см. в разделе [Связанная служба Azure Key Vault](#azure-key-vault-linked-service).
4. Создайте связанную службу хранилища данных, внутри которой указывается соответствующий секрет, хранящийся в хранилище ключей. См. дополнительные сведения об [указании учетных данных, хранимых в хранилище ключей](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Связанная служба Azure Key Vault

Для связанной службы Azure Key Vault поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureKeyVault**. | Да |
| BaseUrl | Укажите URL-адрес Azure Key Vault. | Да |

**Пример**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Указание учетных данных, хранимых в хранилище ключей

Следующие свойства поддерживаются при настройке поля в связанной службе, указывающей секрет хранилища ключей:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type поля необходимо задать значение **AzureKeyVaultSecret**. | Да |
| secretName | Имя секрета в Azure Key Vault. | Да |
| secretVersion | Версия секрета в Azure Key Vault.<br/>Если не указано, используется последняя версия секрета.<br/>Если указано, то он придерживается указанной версии.| Нет |
| store | Ссылается на связанную службу Azure Key Vault, которая используется для хранения учетных данных. | Да |

**Пример: (ознакомьтесь с разделом "Пароль")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.