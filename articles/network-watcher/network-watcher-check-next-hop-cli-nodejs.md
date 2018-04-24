---
title: "Поиск следующего прыжка с помощью возможности определения следующего прыжка в Наблюдателе за сетями Azure (Azure CLI 1.0) | Документация Майкрософт"
description: "В этой статье описано, как определить тип следующего прыжка и IP-адрес, используя возможность определения следующего прыжка в Azure CLI."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Определите тип следующего прыжка, используя возможность определения следующего прыжка в Наблюдателе за сетями Azure в Azure CLI 1.0.

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Определение следующего прыжка — это возможность Наблюдателя за сетями, позволяющая определить тип следующего прыжка и IP-адрес на основе указанной виртуальной машины. Эта возможность используется, чтобы определить путь передачи исходящего трафика виртуальной машины (шлюз, Интернет или виртуальные сети) к месту назначения.

В этой статье используется кроссплатформенной Azure CLI 1.0, доступный для Windows, Mac и Linux.

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии для определения типа следующего прыжка и IP-адреса используется Azure CLI.

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями). Предполагается также, что у вас имеется группа ресурсов с допустимой виртуальной машиной.

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, используется определение следующего прыжка — возможность Наблюдателя за сетями, которая позволяет определить тип следующего перехода и IP-адрес для ресурса. Дополнительные сведения об определении следующего прыжка см. в [этой статье](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Получение следующего прыжка

Чтобы получить следующий прыжок, вызовите командлет `azure netowrk watcher next-hop`. Мы передаем в командлет группу ресурсов Наблюдателя за сетями, Наблюдатель за сетями, идентификатор виртуальной машины, IP-адрес источника и места назначения. В этом примере в качестве IP-адреса места назначения используется IP-адрес виртуальной машины в другой виртуальной сети. Между двумя виртуальными сетями находится шлюз виртуальной сети. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Если в виртуальной машине есть несколько сетевых карт и на любой из них включена IP-пересылка, нужно указать параметр сетевой карты (-i nic-id). В противном случае этот параметр является необязательным.

## <a name="review-results"></a>просмотрите результаты;

По завершении выводятся результаты. Возвращается IP-адрес следующего прыжка и тип ресурса.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

Ниже перечислены доступные значения NextHopType.

**Типы следующего прыжка**

* Интернет
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Нет

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как просмотреть параметры группы безопасности сети программным образом, в статье [NSG Auditing with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Выполнение аудита групп безопасности сети с помощью Наблюдателя за сетями).
