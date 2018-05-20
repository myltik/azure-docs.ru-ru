---
title: Сценарий PowerShell для добавления внешнего пользователя в пользовательскую лабораторию в службе "Службы лабораторий Azure" | Документация Майкрософт
description: Этот сценарий PowerShell добавляет внешнего пользователя в пользовательскую лабораторию в службе "Службы лабораторий Azure".
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
ms.openlocfilehash: b089067a889f0ffd3b317fcc3f0784d176473b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-an-external-user-to-a-custom-lab"></a>Добавление внешнего пользователя в пользовательскую лабораторию с помощью PowerShell

Этот пример сценария PowerShell добавляет внешнего пользователя в пользовательскую лабораторию в службе "Службы лабораторий Azure". 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>предварительным требованиям
* **Пользовательская лаборатория**. Этот сценарий требует наличия пользовательской лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Извлекает объект-пользователь из Azure Active Directory. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | Присваивает указанную роль заданному субъекту в определенной области. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).