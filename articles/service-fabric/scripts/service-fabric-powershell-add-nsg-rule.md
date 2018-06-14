---
title: Пример сценария Azure PowerShell для добавления правила группы безопасности сети | Документация Майкрософт
description: Пример сценария Azure PowerShell для добавления группы безопасности сети, разрешающей входящий трафик через определенный порт.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
ms.locfileid: "25987195"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Добавление правило входящего трафика для группы безопасности сети

Этот пример сценария создает правило группы безопасности сети, разрешающее входящий трафик для порта 8081.  Сценарий возвращает ресурс `Microsoft.Network/networkSecurityGroups`, в котором находится кластер, создает правило конфигурации безопасности сети и обновляет группу безопасности сети. Измените параметры, если это необходимо.

При необходимости установите Azure PowerShell с помощью инструкций, приведенных в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Возвращает ресурс `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Возвращает группу безопасности сети по имени.|
|[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Добавляет в группу безопасности сети конфигурацию правила безопасности сети. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Задает целевое состояние для группы безопасности сети.|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).
