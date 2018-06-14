---
title: Элемент пользовательского интерфейса CredentialsCombo для портала Azure | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Compute.CredentialsCombo для портала Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 914e354265754a05476e96411d35e6cb04183213
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261060"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Compute.CredentialsCombo
Группа элементов управления со встроенной проверкой паролей и открытых ключей SSH для Windows и Linux.

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

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
