---
title: "Поиск следующего прыжка с помощью возможности определения следующего прыжка в Наблюдателе за сетями Azure (Azure CLI 2.0) | Документация Майкрософт"
description: "В этой статье описано, как определить тип следующего прыжка и IP-адрес, используя возможность определения следующего прыжка в Azure CLI."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 873bd0f8c98cfa67c77841df8aa53eb2b895cd54
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>Определите тип следующего прыжка, используя возможность определения следующего прыжка в Наблюдателе за сетями Azure в Azure CLI 2.0.

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Определение следующего прыжка — это возможность Наблюдателя за сетями, позволяющая определить тип следующего прыжка и IP-адрес на основе указанной виртуальной машины. Эта возможность используется, чтобы определить путь передачи исходящего трафика виртуальной машины (шлюз, Интернет или виртуальные сети) к месту назначения.

В этой статье мы используем наш новейший интерфейс командной строки для модели развертывания с помощью Resource Manager, а именно Azure CLI 2.0. Этот инструмент доступен для Windows, Mac и Linux.

Для выполнения действий, описанных в этой статье, требуется [установить интерфейс командной строки Azure для Mac, Linux и Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии для определения типа следующего прыжка и IP-адреса используется Azure CLI.

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями). Предполагается также, что у вас имеется группа ресурсов с допустимой виртуальной машиной.

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, используется определение следующего прыжка — возможность Наблюдателя за сетями, которая позволяет определить тип следующего перехода и IP-адрес для ресурса. Дополнительные сведения об определении следующего прыжка см. в [этой статье](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Получение следующего прыжка

Чтобы получить следующий прыжок, вызовите командлет `az network watcher show-next-hop`. Мы передаем в командлет группу ресурсов Наблюдателя за сетями, Наблюдатель за сетями, идентификатор виртуальной машины, IP-адрес источника и места назначения. В этом примере в качестве IP-адреса места назначения используется IP-адрес виртуальной машины в другой виртуальной сети. Между двумя виртуальными сетями находится шлюз виртуальной сети.

Установите и настройте последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) (если вы еще этого не сделали), а затем войдите с использованием учетной записи Azure, выполнив команду [az login](/cli/azure/reference-index#az_login). Затем выполните следующую команду.

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
Если в виртуальной машине есть несколько сетевых карт и на любой из них включена IP-пересылка, нужно указать параметр сетевой карты (-i nic-id). В противном случае этот параметр является необязательным.

## <a name="review-results"></a>просмотрите результаты;

По завершении выводятся результаты. Возвращается IP-адрес следующего прыжка и тип ресурса.

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
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
