---
title: Квоты виртуальных ЦП для Azure | Документация Майкрософт
description: Дополнительные сведения о квотах виртуальных ЦП для Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>Квоты виртуальных ЦП виртуальной машины

Квоты виртуальных ЦП для виртуальных машин и масштабируемых наборов виртуальных машин разделяются на два уровня для каждой подписки в каждом регионе. Первый уровень — это общие региональные виртуальные ЦП, а второй — ядра для различных семейств размеров виртуальных машин, например виртуальные ЦП семейства Standard D. При развертывании новой виртуальной машины ее виртуальные ЦП не должны превышать квоту на виртуальные ЦП для определенного семейства размеров виртуальных машин или квоту на общие региональные виртуальные ЦП. При превышении любой из этих квот развертывание виртуальной машины будет запрещено. Имеется также квота на общее количество виртуальных машин в регионе. Сведения о каждой из этих квот можно просмотреть в разделе **Использование и квоты** на странице **Подписки** [портала Azure](https://portal.azure.com). Кроме того, можно запросить значения с помощью Azure CLI.


## <a name="check-usage"></a>Проверка использования

Данные об использовании квоты можно проверить с помощью команды [az vm list-usage](/cli/azure/vm#az_vm_list_usage).

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
Зарезервированные экземпляры виртуальных машин, которые ограничиваются одной подпиской, добавляют новый аспект к квотам на виртуальные ЦП. Эти значения описывают число экземпляров указанного размера, которые можно развернуть в подписке. Они выступают в качестве заполнителя в системе квоты, позволяя убедиться, что квота зарезервирована, а также обеспечить возможность развертывания зарезервированных экземпляров в подписке. Например, если в определенной подписке есть 10 зарезервированных экземпляров Standard_D1, лимит использования для них будет составлять 10 единиц. Таким образом в Azure будет обеспечиваться наличие по крайней мере 10 ЦП в рамках квоты на общие региональные виртуальные ЦП, которые будут использоваться для экземпляров Standard_D1, а также наличие хотя бы 10 виртуальных ЦП в рамках квоты на ЦП семейства Standard D, которые будут использоваться для экземпляров Standard_D1.

Если увеличение квоты необходимо для приобретения зарезервированного экземпляра с одной подпиской, вы можете [запросить увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) по подписке.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о выставлении счетов и квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
