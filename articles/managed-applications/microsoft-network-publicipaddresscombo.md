---
title: Элемент пользовательского интерфейса PublicIpAddressCombo для портала Azure | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo для портала Azure.
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: bf0ef5be609fba14ab12e1e6f9f97bc63f032aae
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260566"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo
Группа элементов управления для выбора нового или имеющегося общедоступного IP-адреса.

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
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Если для параметра `constraints.required.domainNameLabel` задано значение **true**, пользователю необходимо указать метку доменного имени при создании общедоступного IP-адреса. Имеющиеся общедоступные IP-адреса без метки недоступны для выбора.
- Если для параметра `options.hideNone` задано значение **true**, значение **Нет** для общедоступного IP-адреса будет скрыто. Значение по умолчанию — **false**.
- Если для параметра `options.hideDomainNameLabel` задано значение **true**, текстовое поле для метки доменного имени будет скрыто. Значение по умолчанию — **false**.
- Если для параметра `options.hideExisting` задано значение true, пользователь не сможет выбрать имеющийся общедоступный IP-адрес. Значение по умолчанию — **false**.
- В качестве значения параметра `zone` можно указать общедоступные IP-адреса указанной зоны или отказоустойчивые в зоне общедоступные IP-адреса.

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
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- Если для `options.hideNone` указать значение **true**, `newOrExistingOrNone` будет иметь только значения **new** или **existing**.
- Если для `options.hideDomainNameLabel` указать значение **true**, `domainNameLabel` не объявляется.

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
