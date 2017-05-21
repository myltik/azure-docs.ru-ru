---
title: "Элемент пользовательского интерфейса Section управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Common.Section для управляемых приложений Azure"
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
ms.openlocfilehash: 7111b02432a774e056b5a5e13f93ae7f11fb6848
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonsection-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.Section
Элемент управления, группирующий один или несколько элементов под заголовком.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Схема
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Параметр `elements` должен содержать по крайней мере один элемент, а также может содержать все типы элементов, кроме `Microsoft.Common.Section`.
- Этот элемент не поддерживает свойство `toolTip`.

## <a name="sample-output"></a>Пример выходных данных
Для доступа к выходным значениям элементов в `elements` используйте функции [basics()](managed-application-createuidefinition-functions.md#basics) или [steps()](managed-application-createuidefinition-functions.md#steps) и точечную нотацию:

```json
basics('section1').element1
```

Элементы типа `Microsoft.Common.Section` не содержат выходные значения.

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

