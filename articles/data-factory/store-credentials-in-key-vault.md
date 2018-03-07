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
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 51318a6bb4f88759984531b005c65712b6817ec0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Хранение учетных данных в Azure Key Vault

Учетные данные для хранилищ данных и вычислительных ресурсов можно хранить в [Azure Key Vault](../key-vault/key-vault-whatis.md). Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных или вычислительный ресурс.

Сейчас эта функция поддерживается для всех видов действий, кроме пользовательских действий. Дополнительные сведения о настройке соединителя см. в разделе "Свойства связанной службы" [в статьях, посвященных каждому типу соединителей](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>предварительным требованиям

Эта функция основывается на удостоверении службы фабрики данных. Узнайте, как работает [удостоверение службы фабрики данных](data-factory-service-identity.md), и убедитесь, что фабрике данных назначено удостоверение.

## <a name="steps"></a>Действия

Чтобы указать учетные данные, хранимые в Azure Key Vault, необходимо:

1. **[Получите удостоверение службы фабрики данных](data-factory-service-identity.md#retrieve-service-identity)**, скопировав значение идентификатора приложения удостоверения службы (SERVICE IDENTITY APPLICATION ID), созданного вместе с фабрикой.
2. **Предоставьте удостоверению службы доступ к Azure Key Vault.** Откройте хранилище ключей, выберите "Политики доступа" и "Добавить новую". Найдите новый идентификатор приложения удостоверения службы, чтобы предоставить разрешение на **получение** в раскрывающемся списке "Разрешения секретов". Это позволит указанной фабрике получить доступ к секрету в хранилище ключей.
3. **Создайте связанную службу, указывающую на Azure Key Vault.** Дополнительные сведения см. в разделе [Связанная служба Azure Key Vault](#azure-key-vault-linked-service).
4. **Создайте связанную службу хранилища данных, внутри которой указывается соответствующий секрет, хранящийся в хранилище ключей.** См. дополнительные сведения об [указании секрета, хранящегося в хранилище ключей](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Связанная служба Azure Key Vault

Для связанной службы Azure Key Vault поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
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

## <a name="reference-secret-stored-in-key-vault"></a>Указание секрета, хранящегося в хранилище ключей

Следующие свойства поддерживаются при настройке поля в связанной службе, указывающей секрет хранилища ключей:

| Свойство | ОПИСАНИЕ | Обязательно |
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

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.