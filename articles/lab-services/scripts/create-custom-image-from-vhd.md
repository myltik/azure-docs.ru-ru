---
title: Сценарий PowerShell для создания пользовательского образа из VHD-файла в службе "Службы лабораторий Azure" | Документация Майкрософт
description: Этот сценарий PowerShell создает пользовательский образ из VHD-файла в службе "Службы лабораторий Azure".
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
ms.openlocfilehash: e63265d7fea18736bf5c85bcc8954a575d70a51f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Создание пользовательского образа из VHD-файла в службе "Службы лабораторий Azure" с помощью PowerShell

Этот пример сценария PowerShell создает пользовательский образ из VHD-файла в службе "Службы лабораторий Azure".

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>предварительным требованиям
* **Пользовательская лаборатория**. Этот сценарий требует наличия пользовательской лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Получает ресурсы. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Выводит список ключей доступа для учетной записи хранения Azure. |
| [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | Добавляет развертывание Azure в группу ресурсов. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).