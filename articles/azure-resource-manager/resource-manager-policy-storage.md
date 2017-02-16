---
title: "Политики Azure Resource Manager для хранилища | Документация Майкрософт"
description: "Описывает политики Azure Resource Manager для управления развертыванием учетных записей хранения."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>Применение политик ресурсов Azure для учетных записей хранения
С помощью политик Azure Resource Manager можно определить согласованные правила развертывания ресурсов в организации. Можно создать настраиваемые политики, чтобы пользователи в организации не нарушали соглашения, которые необходимы для управления ресурсами организации. В этом разделе показано несколько политик, которые определяют правила для учетных записей хранения Azure. Дополнительные сведения о политиках см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).

Примеры в этом демонстрируют жестко запрограммированные значения в правиле политики. Тем не менее для передачи значений можно использовать параметры, которые будут использоваться при назначении политики. Дополнительные сведения можно найти в разделе [Параметры](resource-manager-policy.md#parameters).

## <a name="define-permitted-storage-account-types"></a>Определение разрешенных типов учетных записей хранения

Приведенная ниже политика ограничивает [типы развертываемых учетных записей хранения](../storage/storage-redundancy.md).

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="define-permitted-access-tier"></a>Определение разрешенного уровня доступа

Следующая политика задает тип [уровня доступа](../storage/storage-blob-storage-tiers.md), который можно указать для учетных записей хранения.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Включение шифрования

Следующая политика указывает, что для всех учетных записей хранения должно быть включено [шифрование службы хранилища](../storage/storage-service-encryption.md).

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="create-and-assign-policies"></a>Создание и назначение политик

После определения правила политики (как показано в приведенных выше примерах) необходимо создать политику и назначить ее область. Областью может быть подписка, группа ресурсов или ресурс. Примеры создания и назначения политик приведены в разделе [Создание и назначение политики](resource-manager-policy.md#create-and-assign-a-policy). 

## <a name="next-steps"></a>Дальнейшие действия
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).




<!--HONumber=Jan17_HO3-->


