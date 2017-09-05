---
title: "Элемент пользовательского интерфейса CredentialsCombo управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Compute.CredentialsCombo для управляемых приложений Azure"
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
ms.openlocfilehash: 254f383ee6f7cb9f7051fa135d85319a22c3c369
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Compute.CredentialsCombo
Группа элементов управления со встроенной проверкой паролей и открытых ключей SSH для Windows и Linux. Этот элемент используется при [создании управляемого приложения Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Схема
Если параметр `osPlatform` имеет значение **Windows**, используется следующая схема:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Если параметр `osPlatform` имеет значение **Linux**, используется следующая схема:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Необходимо задать значение для параметра `osPlatform` (**Windows** или **Linux**).
- Если для параметра `constraints.required` задано значение **true**, то текстовые поля для пароля и открытого ключа SSH должны содержать значения, чтобы пройти проверку. Значение по умолчанию — **true**.
- Если для параметра `options.hideConfirmation` задано значение **true**, второе текстовое поле для подтверждения пароля скрыто. Значение по умолчанию — **false**.
- Если для параметра `options.hidePassword` задано значение **true**, возможность использования проверки пароля скрыта. Его можно использовать только тогда, когда параметр `osPlatform` имеет значение **Linux**. Значение по умолчанию — **false**.
- Дополнительные ограничения на разрешенные пароли можно реализовать с помощью свойства `customPasswordRegex`. Строка в `customValidationMessage` отображается, если пароль не прошел пользовательскую проверку. Значение по умолчанию для обоих свойств — **null**.

## <a name="sample-output"></a>Пример выходных данных
Если параметр `osPlatform` имеет значение **Windows** или пользователь указал пароль вместо открытого ключа SSH, ожидаются следующие выходные данные:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Если пользователь предоставил открытый ключ SSH, ожидаются следующие выходные данные:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

