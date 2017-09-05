---
title: "Элемент пользовательского интерфейса Section управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Common.Section для управляемых приложений Azure"
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
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 34c0f2f88e6af5a0f822ec116e7e2334e4e29e8d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcommonsection-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.Section
Элемент управления, группирующий один или несколько элементов под заголовком. Этот элемент используется при [создании управляемого приложения Azure](managed-application-publishing.md).

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
* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

