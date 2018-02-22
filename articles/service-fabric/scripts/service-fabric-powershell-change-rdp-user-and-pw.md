---
title: "Пример сценария Azure PowerShell для обновления имени пользователя и пароля RDP | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для обновления имени пользователя и пароля RDP для всех узлов кластера Service Fabric, относящихся к конкретному типа узла."
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
ms.date: 11/17/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b97cee11c9a85cbd60a05bdbdcd010a0f0a106f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Обновление имени пользователя и пароля администратора виртуальных машин в кластере

Масштабируемый набор виртуальных машин соответствует типу узла в кластере Service Fabric. Этот сценарий обновляет имя пользователя и пароль администратора для виртуальных машин кластера на узле определенного типа.  Добавьте расширение VMAccessAgent в масштабируемый набор, так как пароль администратора является неизменяемым свойством масштабируемого набора.  Изменение имени пользователя и пароля применяется ко всем узлам в масштабируемом наборе. Измените параметры, если это необходимо.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Получает свойства типа узла кластера (масштабируемого набора виртуальных машин).   |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| Добавляет расширение в масштабируемый набор виртуальных машин.|
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Обновляет состояние масштабируемого набора виртуальных машин до состояния локального объекта VMSS.|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
