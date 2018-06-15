---
title: Сброс канала Azure ExpressRoute после сбоя с помощью PowerShell | Документация Майкрософт
description: Эта статья поможет вам сбросить канал ExpressRoute, который находится в неисправном состоянии.
documentationcenter: na
services: expressroute
author: anzaman
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 423bc1d6409e5b7fe02339a05d0775f4ff42de49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31590689"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Сброс канала ExpressRoute после сбоя

Если операция на канале ExpressRoute не выполнена, канал может перейти в состояние сбоя. Эта статья поможет вам сбросить канал Azure ExpressRoute после сбоя.

## <a name="reset-a-circuit"></a>Сброс канала

1. Установите последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

  ```powershell
  Connect-AzureRmAccount
  ```
3. Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Чтобы сбросить канал, который находится в состоянии сбоя, выполните следующие команды:

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Теперь канал должен быть работоспособным. Откройте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), если канал по-прежнему находится в состоянии сбоя.

## <a name="next-steps"></a>Дополнительная информация

Отправьте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , если у вас по-прежнему возникают проблемы.
