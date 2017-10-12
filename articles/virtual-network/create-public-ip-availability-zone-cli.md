---
title: "Создание зонального общедоступного IP-адреса с помощью Azure CLI | Документы Майкрософт"
description: "Создание общедоступного IP-адреса в зоне доступности с помощью интерфейса командной строки Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Создание общедоступного IP-адреса в зоне доступности с помощью интерфейса командной строки Azure.

Общедоступный IP-адрес можно развернуть в зоне доступности Azure (предварительная версия). Зона доступности представляет собой физически отдельную зону в регионе Azure. Ниже перечислено, что вы можете узнать.

> * Создание общедоступного IP-адреса в зоне доступности
> * Определение связанных ресурсов, созданных в зоне доступности

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать интерфейс командной строки (CLI) локально, то для работы с этим руководством вам потребуется версия Azure CLI, следующая за версией 2.0.17. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Зоны доступности используются в режиме предварительной версии в сценариях разработки и тестирования. Поддержка реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Создание зонального общедоступного IP-адреса

Создайте общедоступный IP-адрес в зоне доступности, используя следующую команду:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> При назначении общедоступного IP-адреса стандартных SKU сетевому интерфейсу виртуальной машины необходимо явно разрешить предполагаемый трафик с помощью [группы безопасности сети](security-overview.md#network-security-groups). Обмен данными с ресурсом будет невозможен, пока вы не создадите и не свяжете группу безопасности сети и явно не разрешите нужный трафик.

## <a name="get-zone-information-about-a-public-ip-address"></a>Получение сведений о зоне общедоступного IP-адреса

Получите сведения о зоне общедоступного IP-адреса, используя следующую команду:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [зонах доступности](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Дополнительные сведения об [общедоступных IP-адресах](../virtual-network/virtual-network-public-ip-address.md) 
