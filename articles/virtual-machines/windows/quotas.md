---
title: "квоты виртуальных процессоров для Azure | Документы Microsoft"
description: "Дополнительные сведения о квотах виртуальных процессоров для Azure."
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: b481299b62d452bc306c1f9c1fa2cdccd49b818e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Квоты виртуальных процессоров для виртуальной машины

Квоты виртуальных процессоров для виртуальных машин и наборы масштабирования виртуальных машин располагаются в двух уровнях для каждой подписки в каждом регионе. Первый уровень общее региональные ЦП, который второго уровня различные ВМ размера семейства ядра например Standard семейства D ЦП. Каждый раз при обнаружении новой виртуальной Машины развернуты ЦП для развернутой виртуальной Машины не должен превышать Квота виртуальных процессоров для определенного семейства размер виртуальной Машины или общее региональных виртуальных процессоров. Развертывание виртуальной Машины будет не допускается, при превышении любого из этих квот. Имеется также квоту для общего количества виртуальных машин в области. Сведения о каждой из эти квоты можно увидеть в **использование + квоты** раздел **подписки** страницы в [портал Azure](https://portal.azure.com), или можно запросить с помощью значения PowerShell.

 
## <a name="check-usage"></a>Проверка использования

Можно использовать [Get AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) для проверки на ваш использования квот.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

Результат будет выглядеть примерно так:

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
Зарезервированные экземпляры виртуальной Машины, которая ограничивается только одной подписке, будет добавлен новый аспект квоты виртуальных процессоров. Эти значения описывают число экземпляров указанных выше, размер которой должен быть развернут в подписке. Они работают как заполнитель в системе квоты, чтобы убедиться, что является зарезервированным данной квоты, чтобы убедиться, что зарезервированные экземпляры будут помещены в подписке. Например если определенная подписка имеет 10 Standard_D1 зарезервированные экземпляры, ограничить их использование для зарезервированных экземпляров Standard_D1 будут 10. Это приведет к Azure, чтобы всегда есть по крайней мере 10 ЦП в общее региональные ЦП квоту, которая будет использоваться для экземпляров Standard_D1, и в квоте Стандартная семейства D виртуальных процессоров для экземпляров Standard_D1 доступны по крайней мере 10 ЦП.

Если увеличение квоты необходимо либо приобрести один RI подписки, вы можете [запросить увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) по подписке.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о выставлении счетов и квотах см. в разделе [подписка Azure и ограничения служб, квоты и ограничения](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).