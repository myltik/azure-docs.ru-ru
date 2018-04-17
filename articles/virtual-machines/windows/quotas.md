---
title: Квоты виртуальных ЦП для Azure | Документация Майкрософт
description: Дополнительные сведения о квотах виртуальных ЦП для Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a33aca62c63269ed179b7086cacc3cf758e862ea
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>Квоты виртуальных ЦП виртуальной машины

Квоты виртуальных ЦП для виртуальных машин и масштабируемых наборов виртуальных машин разделяются на два уровня для каждой подписки в каждом регионе. Первый уровень — это общие региональные виртуальные ЦП, а второй — ядра для различных семейств размеров виртуальных машин, например виртуальные ЦП семейства Standard D. При развертывании новой виртуальной машины ее виртуальные ЦП не должны превышать квоту на виртуальные ЦП для определенного семейства размеров виртуальных машин или квоту на общие региональные виртуальные ЦП. При превышении любой из этих квот развертывание виртуальной машины будет запрещено. Имеется также квота на общее количество виртуальных машин в регионе. Сведения о каждой из этих квот можно просмотреть в разделе **Использование и квоты** на странице **Подписки** [портала Azure](https://portal.azure.com). Кроме того, можно запросить значения с помощью PowerShell.

 
## <a name="check-usage"></a>Проверка использования

Проверить использование квот можно с помощью командлета [Get AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage).

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

Выходные данные будут выглядеть следующим образом.

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count

```


## <a name="reserved-vm-instances"></a>Зарезервированные экземпляры виртуальных машин
Зарезервированные экземпляры виртуальных машин, которые ограничиваются одной подпиской, добавляют новый аспект к квотам на виртуальные ЦП. Эти значения описывают число экземпляров указанного размера, которые можно развернуть в подписке. Они выступают в качестве заполнителя в системе квоты, позволяя убедиться, что квота зарезервирована, а также обеспечить возможность развертывания зарезервированных экземпляров в подписке. Например, если в определенной подписке есть 10 зарезервированных экземпляров Standard_D1, лимит использования для них будет составлять 10 единиц. Таким образом в Azure будет обеспечиваться наличие по крайней мере 10 ЦП в рамках квоты на общие региональные виртуальные ЦП, которые будут использоваться для экземпляров Standard_D1, а также наличие хотя бы 10 виртуальных ЦП в рамках квоты на ЦП семейства Standard D, которые будут использоваться для экземпляров Standard_D1.

Если увеличение квоты необходимо для приобретения зарезервированного экземпляра с одной подпиской, вы можете [запросить увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) по подписке.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о выставлении счетов и квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
