---
title: "Элемент пользовательского интерфейса PublicIpAddressCombo управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo для управляемых приложений Azure"
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 94f1f64a57784254912cb6cf568fafc15c8e74ec
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo
Группа элементов управления для выбора нового или имеющегося общедоступного IP-адреса. Этот элемент используется при [создании управляемого приложения Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Если для общедоступного IP-адреса выбрано значение "Нет", текстовое поле метки доменного имени будет скрыто.
- Если выбран имеющийся общедоступный IP-адрес, текстовое поле метки доменного имени будет отключено. Его значение является меткой доменного имени выбранного IP-адреса.
- Суффикс доменного имени (например, westus.cloudapp.azure.com) обновляется автоматически на основе выбранного расположения.

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Если для параметра `constraints.required.domainNameLabel` задано значение **true**, пользователю необходимо указать метку доменного имени при создании общедоступного IP-адреса. Имеющиеся общедоступные IP-адреса без метки недоступны для выбора.
- Если для параметра `options.hideNone` задано значение **true**, значение **Нет** для общедоступного IP-адреса будет скрыто. Значение по умолчанию — **false**.
- Если для параметра `options.hideDomainNameLabel` задано значение **true**, текстовое поле для метки доменного имени будет скрыто. Значение по умолчанию — **false**.
- Если для параметра `options.hideExisting` задано значение true, пользователь не сможет выбрать имеющийся общедоступный IP-адрес. Значение по умолчанию — **false**.

## <a name="sample-output"></a>Пример выходных данных
Если общедоступный IP-адрес не выбран, ожидаются следующие выходные данные:
```json
{
  "newOrExistingOrNone": "none"
}
```

Если выбран новый или имеющийся IP-адрес, ожидаются следующие выходные данные:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- Если для `options.hideNone` указать значение **true**, `newOrExistingOrNone` будет иметь только значения **new** или **existing**.
- Если для `options.hideDomainNameLabel` указать значение **true**, `domainNameLabel` не объявляется.

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения об управляемых приложениях Azure см. в [этой статье](overview.md).
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
