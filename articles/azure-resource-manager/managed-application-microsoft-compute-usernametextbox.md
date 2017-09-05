---
title: "Элемент пользовательского интерфейса UserNameTextBox управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе Microsoft.Compute.UserNameTextBox для управляемых приложений Azure"
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
ms.openlocfilehash: c90be5a0ed3aadda81d7ec9b5388a96472f69af0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Compute.UserNameTextBox
Элемент управления "Текстовое поле" со встроенной проверкой имен пользователей Windows и Linux. Этот элемент используется при [создании управляемого приложения Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Если для параметра `constraints.required` задано значение **true**, то текстовое поле должно содержать значение, чтобы пройти проверку. Значение по умолчанию — **true**.
- Необходимо задать значение для параметра `osPlatform` (**Windows** или **Linux**).
- `constraints.regex` — это шаблон регулярного выражения JavaScript. Если параметр указан, значение текстового поля должно соответствовать шаблону, чтобы пройти проверку. Значение по умолчанию — **null**.
- `constraints.validationMessage` — это строка, которая отображается, когда значение текстового поля не проходит проверку, указанную в `constraints.regex`. Если параметр не указан, используются встроенные сообщения проверки текстового поля. Значение по умолчанию — **null**.
- Этот элемент содержит встроенную проверку, которая основана на значении, заданном для параметра `osPlatform`. Встроенная проверка может использоваться вместе с настраиваемым регулярным выражением.
Если для параметра `constraints.regex` значение указано, активируются встроенные и пользовательские проверки.

## <a name="sample-output"></a>Пример выходных данных
```json
"tabrezm"
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

