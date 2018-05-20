---
title: Пример скрипта Azure PowerShell. Настройка политики с ограничением скорости | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Настройка политики с ограничением скорости
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: d238ca2b4966fc84ad633bf547fa578a3e8b1e38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="set-up-rate-limit-policy"></a>Настройка политики с ограничением скорости

В этом скрипте настраивается политика с ограничением скорости. 

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell версии 3.6 или более поздней. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/api-management/setup-rate-limit-policy/setup_rate_limit_policy.ps1 "Set up rate limit policy")]

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Дополнительные примеры скриптов Azure PowerShell для службы управления API Azure см. в [этой статье](../powershell-samples.md).
