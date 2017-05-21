---
title: "Элемент пользовательского интерфейса StorageAccountSelector управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Storage.StorageAccountSelector для управляемых приложений Azure"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2d64cfcd958f17171e9ef1a38777f202bdfda774
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Storage.StorageAccountSelector
Элемент управления для выбора новой или имеющейся учетной записи хранения.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Если необходимо, параметр `defaultValue.name` автоматически проверяется на уникальность. Если имя учетной записи хранения не является уникальным, пользователь должен указать другое имя или выбрать имеющуюся учетную запись хранения.
- Значением по умолчанию для параметра `defaultValue.type` является **Premium_LRS**.
- Любой тип, не указанный в `constraints.allowedTypes`, скрыт, а любой тип, не указанный в `constraints.excludedTypes`, отображается.
Параметры `constraints.allowedTypes` и `constraints.excludedTypes` являются необязательными. При этом их нельзя использовать одновременно.
- Если для параметра `options.hideExisting` задано значение **true**, пользователь не может выбрать имеющуюся учетную запись хранения. Значение по умолчанию — **false**.


## <a name="sample-output"></a>Пример выходных данных
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

