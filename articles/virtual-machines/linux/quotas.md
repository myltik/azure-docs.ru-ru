---
title: "квоты виртуальных процессоров для Azure | Документы Microsoft"
description: "Дополнительные сведения о квотах виртуальных процессоров для Azure."
keywords: 
services: virtual-machines-linux
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: 8b99fd92d9031b7172e698cf5db1f776387bdfb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Квоты виртуальных процессоров для виртуальной машины

Квоты виртуальных процессоров для виртуальных машин и наборы масштабирования виртуальных машин располагаются в двух уровнях для каждой подписки в каждом регионе. Первый уровень общее региональные ЦП, который второго уровня различные ВМ размера семейства ядра например Standard семейства D ЦП. Каждый раз при обнаружении новой виртуальной Машины развернуты ЦП для развернутой виртуальной Машины не должен превышать Квота виртуальных процессоров для определенного семейства размер виртуальной Машины или общее региональных виртуальных процессоров. Развертывание виртуальной Машины будет не допускается, при превышении любого из этих квот. Имеется также квоту для общего количества виртуальных машин в области. Сведения о каждой из эти квоты можно увидеть в **использование + квоты** раздел **подписки** страницы в [портал Azure](https://portal.azure.com), или вы можете запрашивать значения, с помощью Azure CLI .


## <a name="check-usage"></a>Проверка использования

Можно проверить с помощью квот использования [списка — использование виртуальных машин az](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Зарезервированные экземпляры виртуальных машин
Зарезервированные экземпляры виртуальной Машины, которая ограничивается только одной подписке, будет добавлен новый аспект квоты виртуальных процессоров. Эти значения описывают число экземпляров указанных выше, размер которой должен быть развернут в подписке. Они работают как заполнитель в системе квоты, чтобы убедиться, что является зарезервированным данной квоты, чтобы убедиться, что зарезервированные экземпляры будут помещены в подписке. Например если определенная подписка имеет 10 Standard_D1 зарезервированные экземпляры, ограничить их использование для зарезервированных экземпляров Standard_D1 будут 10. Это приведет к Azure, чтобы всегда есть по крайней мере 10 ЦП в общее региональные ЦП квоту, которая будет использоваться для экземпляров Standard_D1, и в квоте Стандартная семейства D виртуальных процессоров для экземпляров Standard_D1 доступны по крайней мере 10 ЦП.

Если увеличение квоты необходимо либо приобрести один RI подписки, вы можете [запросить увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) по подписке.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о выставлении счетов и квотах см. в разделе [подписка Azure и ограничения служб, квоты и ограничения](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
