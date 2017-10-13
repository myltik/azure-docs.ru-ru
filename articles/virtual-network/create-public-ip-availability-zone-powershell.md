---
title: "Создание зонального общедоступного IP-адреса с помощью PowerShell | Документы Майкрософт"
description: "Создание общедоступного IP-адреса в зоне доступности с помощью PowerShell."
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
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Создание общедоступного IP-адреса в зоне доступности с помощью PowerShell.

Общедоступный IP-адрес можно развернуть в зоне доступности Azure (предварительная версия). Зона доступности представляет собой физически отдельную зону в регионе Azure. Ниже перечислено, что вы можете узнать.

> * Создание общедоступного IP-адреса в зоне доступности
> * Определение связанных ресурсов, созданных в зоне доступности
  

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Чтобы выполнить процедуры, описанные в этой статье, установите модуль AzureRM версии 4.4.0 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если требуется установка или обновление, установите последнюю версию модуля AzureRM из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Зоны доступности используются в режиме предварительной версии в сценариях разработки и тестирования. Поддержка реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Login-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *westeurope*. *westeurope* является одним из регионов Azure, который поддерживает зоны доступности в предварительной версии.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Создание зонального общедоступного IP-адреса

Создайте общедоступный IP-адрес в зоне доступности, используя следующую команду:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> При назначении общедоступного IP-адреса стандартных SKU сетевому интерфейсу виртуальной машины необходимо явно разрешить предполагаемый трафик с помощью [группы безопасности сети](security-overview.md#network-security-groups). Обмен данными с ресурсом будет невозможен, пока вы не создадите и не свяжете группу безопасности сети и явно не разрешите нужный трафик.

## <a name="get-zone-information-about-a-public-ip-address"></a>Получение сведений о зоне общедоступного IP-адреса

Получите сведения о зоне общедоступного IP-адреса, используя следующую команду:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [зонах доступности](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- Дополнительные сведения об [общедоступных IP-адресах](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 