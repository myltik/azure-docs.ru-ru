---
title: Элемент пользовательского интерфейса VirtualNetworkCombo для портала Azure | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Network.VirtualNetworkCombo для портала Azure.
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
ms.openlocfilehash: 5d806afbfd74d68d139f494c7a5a6e871a7dae36
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260600"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Network.VirtualNetworkCombo
Группа элементов управления для выбора новой или имеющейся виртуальной сети.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- В верхней области каркаса пользователь выбрал новую виртуальную сеть. Теперь он может настроить префикс адреса и имя каждой подсети. Настройка подсетей в этом случае является необязательной.
- В нижней области каркаса пользователь выбрал имеющуюся виртуальную сеть. Теперь он должен сопоставить каждую подсеть, необходимую шаблону развертывания, с имеющейся подсетью. Настройка подсетей в этом случае является обязательной.

## <a name="schema"></a>Схема
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- Если указан, первый префикс адреса размера `defaultValue.addressPrefixSize`, который не перекрывается, автоматически определяется на основе имеющихся виртуальных сетей в подписке пользователя.
- Значение по умолчанию для параметров `defaultValue.name` и `defaultValue.addressPrefixSize` — **null**.
- Обязательно должен быть указан параметр `constraints.minAddressPrefixSize`. Любые имеющиеся виртуальные сети с адресным пространством меньше указанного значения являются недоступными.
- Для каждой подсети должны быть определены `subnets` и `constraints.minAddressPrefixSize`.
- При создании виртуальной сети префикс адреса каждой подсети определяется автоматически на основе префикса адреса виртуальной сети и `addressPrefixSize` соответственно.
- При использовании имеющейся виртуальной сети любые подсети со значением меньше, чем у `constraints.minAddressPrefixSize`, — недоступны. Кроме того (если указано), подсети, которые не содержат минимальное число доступных адресов (`minAddressCount`), — недоступны.
Значение по умолчанию — **0**. Чтобы адреса были связанными, задайте значение **true** для `requireContiguousAddresses`. Значение по умолчанию — **true**.
- Создание подсетей в имеющейся виртуальной сети не поддерживается.
- Если для параметра `options.hideExisting` задано значение **true**, пользователь не может выбрать имеющуюся виртуальную сеть. Значение по умолчанию — **false**.

## <a name="sample-output"></a>Пример выходных данных
```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
