---
title: "Поиск следующего прыжка с помощью возможности определения следующего прыжка Наблюдателя за сетями Azure (PowerShell) | Документация Майкрософт"
description: "В этой статье описано, как определить тип следующего прыжка и IP-адрес, используя возможность определения следующего прыжка с помощью PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Определите тип следующего прыжка, используя возможность определения следующего прыжка Наблюдателя за сетями Azure с помощью PowerShell.

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Определение следующего прыжка — это возможность Наблюдателя за сетями, позволяющая определить тип следующего прыжка и IP-адрес на основе указанной виртуальной машины. Эта возможность используется, чтобы определить путь передачи исходящего трафика виртуальной машины (шлюз, Интернет или виртуальные сети) к месту назначения.

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии для определения типа следующего прыжка и IP-адреса используется портал Azure.

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями). Предполагается также, что у вас имеется группа ресурсов с допустимой виртуальной машиной.

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, используется определение следующего прыжка — возможность Наблюдателя за сетями, которая позволяет определить тип следующего перехода и IP-адрес для ресурса. Дополнительные сведения об определении следующего прыжка см. в [этой статье](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Извлечение Наблюдателя за сетями

Сначала необходимо получить экземпляр Наблюдателя за сетями. Переменная `$networkWatcher` передается в командлет проверки следующего прыжка.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Получение виртуальной машины

Функция определения следующего прыжка возвращает сведения о следующем прыжке из виртуальной машины и его IP-адрес. Для командлета требуется идентификатор виртуальной машины. Если вы уже знаете идентификатор виртуальной машины, этот шаг можно пропустить.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Для получения следующего прыжка необходимо, чтобы ресурс виртуальной машины был выделен.

## <a name="get-the-network-interfaces"></a>Получение сетевых интерфейсов

Вам понадобится IP-адрес сетевой карты на виртуальной машине. В этом примере мы получаем сетевые карты на виртуальной машине. Если вы уже знаете IP-адрес, который необходимо протестировать на виртуальной машине, этот шаг можно пропустить.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Получение следующего прыжка

Теперь необходимо вызвать командлет `Get-AzureRmNetworkWatcherNextHop`. Мы передаем в этот командлет Наблюдатель за сетями, идентификатор виртуальной машины, IP-адрес источника и места назначения. В этом примере в качестве IP-адреса места назначения используется IP-адрес виртуальной машины в другой виртуальной сети. Между двумя виртуальными сетями находится шлюз виртуальной сети.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Просмотр результатов

По завершении выводятся результаты. Возвращается IP-адрес следующего прыжка и тип ресурса. В этом случае это общедоступный IP-адрес шлюза виртуальной сети.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

Ниже перечислены доступные значения NextHopType.

**Типы следующего прыжка**

* Интернет
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как просмотреть параметры группы безопасности сети программным образом, в статье [NSG Auditing with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Выполнение аудита групп безопасности сети с помощью Наблюдателя за сетями).

















