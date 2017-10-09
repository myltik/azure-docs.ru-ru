---
title: "Хранение учетных данных в Azure Key Vault | Документация Майкрософт"
description: "Узнайте, как хранить учетные данные для хранилищ данных, используемых в Azure Key Vault, которые фабрика данных Azure может автоматически извлекать в среду выполнения."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Хранение учетных данных в Azure Key Vault

Учетные данные для хранилищ данных можно хранить в [Azure Key Vault](../key-vault/key-vault-whatis.md). Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных.

> [!NOTE]
> В настоящее время только [соединитель Dynamics](connector-dynamics-crm-office-365.md) поддерживает эту функцию. 

## <a name="steps"></a>Действия

При создании фабрики данных создается удостоверение службы. Это управляемое приложение, зарегистрированное в Azure Activity Directory и представляющее эту определенную фабрику данных. Сведения об удостоверении службы можно найти на портале Azure "ваша фабрика данных" > "Свойства": 

- ИДЕНТИФИКАТОР УДОСТОВЕРЕНИЯ СЛУЖБЫ
- КЛИЕНТ УДОСТОВЕРЕНИЯ СЛУЖБЫ
- ИДЕНТИФИКАТОР ПРИЛОЖЕНИЯ УДОСТОВЕРЕНИЯ СЛУЖБЫ

Чтобы указать учетные данные, хранимые в Azure Key Vault, необходимо:

1. Скопировать идентификатор приложения удостоверения службы, созданный вместе с фабрикой данных.
2. Предоставьте удостоверению службы доступ к Azure Key Vault. В "хранилище ключей -> Управление доступом -> Добавить" найдите идентификатор приложения удостоверения службы, чтобы добавить разрешения на чтение. Это позволит указанной фабрике получить доступ к секрету в хранилище ключей.
3. Создайте связанную службу, указывающую на Azure Key Vault. Дополнительные сведения см. в разделе [Связанная служба Azure Key Vault](#azure-key-vault-linked-service).
4. Создайте связанную службу хранилища данных, внутри которой указывается соответствующий секрет, хранящийся в хранилище ключей. Дополнительные сведения см. в разделе [Указание учетных данных, хранимых в хранилище ключей](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Связанная служба Azure Key Vault

Для связанной службы Azure Key Vault поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureKeyVault**. | Да |
| BaseUrl | Укажите URL-адрес Azure Key Vault (DNS-имя). | Да |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
