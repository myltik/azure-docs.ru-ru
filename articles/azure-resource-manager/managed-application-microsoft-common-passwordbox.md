---
title: "Элемент пользовательского интерфейса PasswordBox управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Common.PasswordBox для управляемых приложений Azure"
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
ms.openlocfilehash: 196a4b8f77145f83e46b4b23e148bb3a9dffc1b5
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.PasswordBox
Элемент управления, который может использоваться для указания и подтверждения пароля. Этот элемент используется при [создании управляемого приложения Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Этот элемент не поддерживает свойство `defaultValue`.
- Дополнительные сведения о реализации `constraints` см. в статье [Элемент пользовательского интерфейса Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md).
- Если для параметра `options.hideConfirmation` задано значение **true**, второе текстовое поле для подтверждения пароля скрыто. Значение по умолчанию — **false**.

## <a name="sample-output"></a>Пример выходных данных
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

