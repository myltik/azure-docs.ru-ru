---
title: "Элемент пользовательского интерфейса SizeSelector управляемого приложения Azure | Документация Майкрософт"
description: "Сведения об элементе пользовательского интерфейса Microsoft.Compute.SizeSelector для управляемых приложений Azure"
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
ms.date: 05/12/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e54962f73028ada258a7faef271d66f0fbcef649
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017


---
# <a name="microsoftcomputesizeselector-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector
Элемент управления для выбора размера одного или нескольких экземпляров виртуальной машины. Этот элемент используется при [создании управляемого приложения Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": []
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Параметр `recommendedSizes` должен содержать по крайней мере один размер. По умолчанию используется первый рекомендуемый размер.
- Если в выбранном расположении рекомендуемый размер недоступен, он автоматически пропускается. Вместо него используется следующий рекомендуемый размер.
- Любой размер, не указанный в параметре `constraints.allowedSizes`, скрыт. При этом любой размер, не указанный в параметре `constraints.excludedSizes`, отображается.
Параметры `constraints.allowedSizes` и `constraints.excludedSizes` являются необязательными. При этом их нельзя использовать одновременно. Сведения о получении списка доступных размеров виртуальных машин для подписки см. в [этой статье](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- Необходимо задать значение для параметра `osPlatform` (**Windows** или **Linux**). Он используется для определения затрат на оборудование виртуальных машин.
- Параметр `imageReference` не указывается для основных образов, но указывается для сторонних. Он используется для определения затрат на программное обеспечение виртуальных машин.
- Параметр `count` используется для задания соответствующего числа для элемента. Он поддерживает статическое значение, например **2**, или динамическое значение из другого элемента, например `[steps('step1').vmCount]`. Значение по умолчанию — **1**.

## <a name="sample-output"></a>Пример выходных данных
```json
"Standard_D1"
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md).

