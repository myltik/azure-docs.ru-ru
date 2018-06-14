---
title: Элемент пользовательского интерфейса Azure AvailabilityZoneDropdown | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Network.AvailabilityZoneDropdown для портала Azure.
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
ms.openlocfilehash: 737971551e4bfb81c4f5fd1b53987b7a52c12669
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261077"
---
# <a name="microsoftnetworkavailabilityzonedropdown-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Network.AvailabilityZoneDropdown
Элемент управления, с помощью которого можно выбрать зону доступности.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Microsoft.Network.AvailabilityZoneDropDown](./media/managed-application-elements/microsoft.network.availabilityzonedropdown.png)

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Network.AvailabilityZoneDropdown",
  "label": "Availability zone dropdown label",
  "toolTip": "This is the tooltip for availability zones",
  "defaultValue": "None",
  "constraints": {
    "required": "true"
  },
  "options": {
  },
  "visible": true
}
```

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
