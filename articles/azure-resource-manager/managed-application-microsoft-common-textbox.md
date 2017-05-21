---
title: "Элемент пользовательского интерфейса TextBox управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Common.TextBox для управляемых приложений Azure"
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
ms.openlocfilehash: aa161d2f6611cd7f1ef8fac8eebfde12eedd9fcf
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommontextbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.TextBox
Элемент управления, который может использоваться для редактирования неформатированного текста.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Если для параметра `constraints.required` задано значение **true**, то текстовое поле должно содержать значение, чтобы пройти проверку. Значение по умолчанию — **false**.
- `constraints.regex` — это шаблон регулярного выражения JavaScript. Если параметр указан, значение текстового поля должно соответствовать шаблону, чтобы пройти проверку. Значение по умолчанию — **null**.
- `constraints.validationMessage` — это строка, которая отображается, когда значение в текстовом поле не проходит проверку. Если параметр не указан, используются встроенные сообщения проверки текстового поля. Значение по умолчанию — **null**.
- Можно указать значение для `constraints.regex`, если для параметра `constraints.required` задано значение **false**. В этом случае, чтобы пройти проверку, значение для текстового поля не требуется. Если указано, оно должно соответствовать шаблону регулярного выражения.

## <a name="sample-output"></a>Пример выходных данных

```json
"foobar"
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

