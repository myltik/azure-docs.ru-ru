---
title: Элемент пользовательского интерфейса Azure TextBlock | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.TextBlock для портала Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: f353aceb3cbd283d0ad3cc9d42e85e3f5b63678e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftcommontextblock-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.TextBlock
Элемент управления, с помощью которого можно добавить текст в интерфейс портала.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Схема
```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Look! Arbitrary text in templates!",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
"Look! Arbitrary text in templates! Learn more"
```

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
