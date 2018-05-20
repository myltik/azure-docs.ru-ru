---
title: Элемент пользовательского интерфейса Azure InfoBox | Документация Майкрософт
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
ms.openlocfilehash: 2d0e64364ae7f14a7bfc7772d31f6c2ba1104c51
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftcommoninfobox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.InfoBox
Элемент управления, который добавляет поле сведений.

## <a name="schema"></a>Схема
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Примечания

* Установите для параметра `icon` одно из следующих значений: **Нет**, **Сведения**, **Предупреждение** или **Ошибка**.
* Свойство `uri` необязательное.

## <a name="sample-output"></a>Пример выходных данных

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
