---
title: "Пример сценария Azure PowerShell для изменения диапазона портов RDP | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для изменения диапазона портов RDP развернутого кластера."
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9089d071c69830323a94140a5ebb4e9e865499c6
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-rdp-port-range-values"></a>Обновление значений диапазона портов RDP

Этот сценарий изменяет значения диапазона портов RDP на виртуальных машинах узла кластера после развертывания кластера.  Используется Azure PowerShell, чтобы порты базовых виртуальных машин не сменялись циклически.  Сценарий возвращает ресурс `Microsoft.Network/loadBalancers` в группе ресурсов кластера и обновляет значения `inboundNatPools.frontendPortRangeStart` и `inboundNatPools.frontendPortRangeEnd`. Измените параметры, если это необходимо.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Возвращает ресурс `Microsoft.Network/loadBalancers`. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Обновляет ресурс `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
