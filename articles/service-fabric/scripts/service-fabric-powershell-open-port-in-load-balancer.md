---
title: "Пример сценария Azure PowerShell — открытие порта приложения в подсистеме балансировки нагрузки | Документация Майкрософт"
description: "Пример сценария Azure PowerShell — открытие порта в Azure Load Balancer для приложения Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 08/15/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 76c1e44d7ad2325ce695dd134cf891727d385610
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Открытие порта приложения в Azure Load Balancer

Приложения Service Fabric, работающие в Azure, находятся за Azure Load Balancer. Этот пример сценария открывает порт в Azure Load Balancer, чтобы приложение Service Fabric могли взаимодействовать с внешними клиентами. Измените параметры, если это необходимо. 

При необходимости установите модуль PowerShell ServiceFabric вместе с [пакетом SDK для Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Получает ресурс Azure.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Получает Azure Load Balancer. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Добавляет конфигурацию пробы для подсистемы балансировки нагрузки.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Получает конфигурацию пробы для подсистемы балансировки нагрузки. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Добавляет правило конфигурации в подсистему балансировки нагрузки. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Задает состояние цели для подсистемы балансировки нагрузки. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
