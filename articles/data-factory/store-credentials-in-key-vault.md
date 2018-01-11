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
ms.date: 12/14/2017
ms.author: jingwang
ms.openlocfilehash: 145c2bc0556010389e78e523fde6fd4b9063f930
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Хранение учетных данных в Azure Key Vault

Учетные данные для хранилищ данных можно хранить в [Azure Key Vault](../key-vault/key-vault-whatis.md). Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных.

В настоящее время [соединитель Dynamics](connector-dynamics-crm-office-365.md), [соединителю Salesforce](connector-salesforce.md) и несколько вновь включить поддержку соединители эту функцию. Ожидается, что дополнительные ожидается в более поздней версии. Вы можете проверить каждый раздел соединителя на подробные сведения. Секретный поля, которые поддерживают эту функцию, вы увидите Примечание в описание говорят "*вы можете пометить это поле в виде строки SecureString безопасно хранить в ADF, или сохранить пароль в хранилище ключей Azure и позволить запросу устраняйте копирования оттуда. При выполнении копирования данных — Дополнительные сведения из хранилища учетных данных в хранилище ключей.* "

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Технические условия

Эта функция основывается на удостоверении службы фабрики данных. Узнайте, как работает [удостоверение службы фабрики данных](data-factory-service-identity.md), и убедитесь, что фабрике данных назначено удостоверение.

## <a name="steps"></a>Действия

Чтобы указать учетные данные, хранимые в Azure Key Vault, необходимо:

1. [Получите удостоверение службы фабрики данных](data-factory-service-identity.md#retrieve-service-identity), скопировав значение идентификатора приложения удостоверения службы (SERVICE IDENTITY APPLICATION ID), созданного вместе с фабрикой.
2. Предоставьте удостоверению службы доступ к Azure Key Vault. Выберите элементы "Хранилище ключей -> Управление доступом -> Добавить". Найдите идентификатор приложения удостоверения службы, чтобы добавить разрешения на **чтение**. Это позволит указанной фабрике получить доступ к секрету в хранилище ключей.
3. Создайте связанную службу, указывающую на Azure Key Vault. Дополнительные сведения см. в разделе [Связанная служба Azure Key Vault](#azure-key-vault-linked-service).
4. Создайте связанную службу хранилища данных, внутри которой указывается соответствующий секрет, хранящийся в хранилище ключей. См. дополнительные сведения об [указании учетных данных, хранимых в хранилище ключей](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Связанная служба Azure Key Vault

Для связанной службы Azure Key Vault поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureKeyVault**. | Yes |
| BaseUrl | Укажите URL-адрес Azure Key Vault. | Yes |

**Пример.**

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

| Свойство | ОПИСАНИЕ | Требуется |
|:--- |:--- |:--- |
| Тип | Для свойства type поля необходимо задать значение **AzureKeyVaultSecret**. | Yes |
| secretName | Имя секрета в Azure Key Vault. | Yes |
| secretVersion | Версия секрета в Azure Key Vault.<br/>Если не указано, используется последняя версия секрета.<br/>Если указано, то он придерживается указанной версии.| Нет  |
| store | Ссылается на связанную службу Azure Key Vault, которая используется для хранения учетных данных. | Yes |

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