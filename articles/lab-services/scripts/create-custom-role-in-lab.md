---
title: Сценарий PowerShell для создания настраиваемой роли в пользовательской лаборатории в службе "Службы лабораторий Azure" | Документация Майкрософт
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
ms.openlocfilehash: df91c9f842d338e1725fec2734129f2f1f3d3721
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-role-in-a-custom-lab-in-azure-lab-services"></a>Создание настраиваемой роли в пользовательской лаборатории в службе "Службы лабораторий Azure" с помощью PowerShell

Этот пример сценария PowerShell создает настраиваемую роль для использования в пользовательской лаборатории в службе "Службы лабораторий Azure". 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>предварительным требованиям
* **Пользовательская лаборатория**. Этот сценарий требует наличия пользовательской лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Возвращает операции для поставщика ресурсов Azure, защищаемые с помощью Azure RBAC. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Выводит список всех ролей Azure RBAC, доступных для назначения. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Создает настраиваемую роль. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).