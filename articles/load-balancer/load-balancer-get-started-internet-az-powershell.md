---
title: "Создание общедоступной подсистемы балансировки нагрузки уровня \"Стандартный\" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью PowerShell | Документация Майкрософт"
description: "Сведения о создании общедоступной подсистемы балансировки нагрузки уровня \"Стандартный\" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью PowerShell."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2945e04814f3d1e5b281391485def47f4b463d88
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Создание общедоступной подсистемы балансировки нагрузки уровня "Стандартный" с избыточным в пределах зоны интерфейсным общедоступным IP-адресом с помощью PowerShell

В этой статье приведены действия по созданию общедоступной [подсистемы балансировки нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным в пределах зоны интерфейсным сервером с помощью общедоступного стандартного IP-адреса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Регистрация для использования зон доступности, подсистемы балансировки нагрузки уровня "Стандартный" и общедоступного стандартного IP-адреса (предварительная версия)

Чтобы выполнить процедуры, описанные в этой статье, установите модуль AzureRM версии 4.4.0 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если требуется установка или обновление, установите последнюю версию модуля AzureRM из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
[Номер SKU подсистемы балансировки нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) сейчас находится в предварительной версии. На период действия предварительной версии эта функция может не отличаться таким же уровнем доступности и надежности, как функции, предоставляемые в режиме общедоступной версии. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Общедоступный номер SKU подсистемы балансировки нагрузки уровня "Базовый"](load-balancer-overview.md) можно использовать для производственных служб. 

> [!NOTE]
> Зоны доступности используются в режиме предварительной версии в сценариях разработки и тестирования. Поддержка реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются с зонами доступности, см. в статье [Общие сведения о зонах доступности в Azure (предварительная версия)](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Перед выбором зоны или избыточного в пределах зоны параметра интерфейсного общедоступного IP-адреса для подсистемы балансировки нагрузки необходимо выполнить действия, описанные в статье [Общие сведения о зонах доступности в Azure (предварительная версия)](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Login-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Чтобы создать группу ресурсов, выполните следующую команду:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Создание общедоступного стандартного IP-адреса 
Создайте общедоступный стандартный IP-адрес с помощью следующей команды:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Создание интерфейсной IP-конфигурации веб-сайта

Создайте внешнюю IP-конфигурацию с помощью следующей команды:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Создание пула внутренних адресов

Создайте пул внутренних адресов с помощью следующей команды:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Создание пробы работоспособности подсистемы балансировки нагрузки в порте 80

Создайте пробу работоспособности подсистемы балансировки нагрузки в порте 80 с помощью следующей команды:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки
Создайте подсистему балансировки нагрузки уровня "Стандартный" с помощью следующей команды:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте о [создании общедоступного IP-адреса в зоне доступности](../virtual-network/create-public-ip-availability-zone-portal.md).




