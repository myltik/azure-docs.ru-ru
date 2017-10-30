---
title: "Ошибки имени учетной записи хранения Azure | Документация Майкрософт"
description: "Описание ошибок, которые могут возникнуть при указании имени учетной записи хранения."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>Устранение ошибок имен учетной записи хранения

В этой статье описываются ошибки именования, которые могут возникнуть при развертывании учетной записи хранения.

## <a name="symptom"></a>Симптом

Если имя учетной записи хранения содержит запрещенные знаки, возникает ошибка. Пример такой ошибки показан ниже.

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Для учетных записей хранения необходимо указывать имя ресурса, уникальное в среде Azure. Если не указать уникальное имя, возникнет такая ошибка:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Если при развертывании учетной записи хранения указать для нее то же имя, что и для существующей учетной записи хранения в подписке, но указать другое расположение, появится сообщение о том, что учетная запись хранения уже существует в другом расположении. Удалите существующую учетную запись хранения или укажите то же расположение, что и для существующей учетной записи хранения.

## <a name="cause"></a>Причина:

Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и букв нижнего регистра. Имя должно быть уникальным.

## <a name="solution"></a>Решение

### <a name="solution-1"></a>Решение 1

Убедитесь, что имя учетной записи хранения является уникальным. Уникальное имя можно создать, используя соглашение об именовании и результат функции [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>Решение 2

Убедитесь, что имя учетной записи хранения содержит не больше 24 знаков. Функция [UniqueString](resource-group-template-functions-string.md#uniquestring) возвращает 13 знаков. Если используется сцепка префикса или постфикса с результатом **uniqueString**, то следует использовать значение, содержащие не более 11 знаков.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

### <a name="solution-3"></a>Решение 3

Убедитесь, что имя учетной записи хранения не содержит буквы в верхнем регистре или специальные знаки.