---
title: Сценарий PowerShell для задания допустимых размеров виртуальных машин в службе "Службы лабораторий Azure" | Документация Майкрософт
description: Этот сценарий PowerShell задает допустимые размеры виртуальных машин в службе "Службы лабораторий Azure".
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 2fc7001e5594b1c37e6566cde1ee586194737e3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Задание допустимых размеров виртуальных машин в службе "Службы лабораторий Azure" с помощью PowerShell

Этот пример сценария PowerShell задает допустимые размеры виртуальных машин в службе "Службы лабораторий Azure".

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>предварительным требованиям
* **Пользовательская лаборатория**. Этот сценарий требует наличия пользовательской лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Заметки |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Выполняет поиск ресурсов на основе указанных параметров. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Получает ресурсы. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Изменяет ресурс. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Создает ресурса. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).