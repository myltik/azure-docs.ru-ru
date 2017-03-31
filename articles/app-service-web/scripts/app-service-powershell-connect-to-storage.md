---
title: "Пример сценария Azure PowerShell. Подключение веб-приложения к учетной записи хранения | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для подключения веб-приложения к учетной записи хранения."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 05b0463704abcb33f9a967beb276c5a9422b4908
ms.lasthandoff: 03/21/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>Подключение веб-приложения к учетной записи хранения

В этой статье вы узнаете, как создать учетную запись хранения и веб-приложение Azure. Затем вы свяжете учетную запись хранения с веб-приложением, используя параметры приложения.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), а затем выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Подключение веб-приложения к учетной записи хранения")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, веб-приложение и все связанные ресурсы, используя следующую команду.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Создает план службы приложений. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Создает веб-приложение. |
| [New-AzureRMStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/New-AzureRmStorageAccount) | Создает учетную запись хранения. |
| [Get-AzureRMStorageAccountKey](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/get-azurermstorageaccountkey) | Выводит список ключей доступа для учетной записи хранения Azure. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Изменяет конфигурацию веб-приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../app-service-powershell-samples.md).

