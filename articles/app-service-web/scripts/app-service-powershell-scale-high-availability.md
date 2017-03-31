---
title: "Пример сценария Azure PowerShell. Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для глобального масштабирования веб-приложения с помощью высокодоступной архитектуры."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e64b4f3bfc16de82e0f9d900b2dcc4b0ca11e3cb
ms.lasthandoff: 03/21/2017

---

# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры

В этом сценарии вы создадите группу ресурсов, два плана службы приложений, два веб-приложения, профиль и две конечные точки диспетчера трафика. Завершив его, вы получите высокодоступную архитектуру, обеспечивающую глобальную доступность веб-приложения на основе минимальной задержки сети.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), а затем выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры")]

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
| [New-AzureRMTrafficManagerProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.trafficmanager/v2.5.0/new-azurermtrafficmanagerprofile) | Создает профиль диспетчера трафика. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Создает план службы приложений. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Создает веб-приложение. |
| [New-AzureRMTrafficManagerEndpoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.trafficmanager/v2.5.0/new-azurermtrafficmanagerendpoint) | Создает конечную точку в профиле диспетчера трафика Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../app-service-powershell-samples.md).

