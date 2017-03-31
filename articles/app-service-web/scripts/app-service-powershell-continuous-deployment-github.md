---
title: "Пример сценария Azure PowerShell. Создание веб-приложения с непрерывным развертыванием из GitHub | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для создания веб-приложения с непрерывным развертыванием из GitHub."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b6b82aa6f129a008091e4abc1798b9b5d402c28e
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Создание веб-приложения с непрерывным развертыванием из GitHub

Этот пример скрипта создает веб-приложение в службе приложений со связанными ресурсами, а затем настраивает непрерывное развертывание из репозитория GitHub. Дополнительные сведения о развертывании GitHub без непрерывного развертывания см. в статье [Создание веб-приложения и развертывание кода из GitHub](app-service-powershell-deploy-github.md).

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/). Кроме того, убедитесь в следующем.

- Подключение к Azure установлено с помощью команды `az login`.
- Код приложения находится в вашем открытом или закрытом репозитории GitHub.
- Вы [создали маркер доступа в учетной записи GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Создание веб-приложения с непрерывным развертыванием из GitHub")]

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
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | Изменяет ресурс в группе ресурсов. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../app-service-powershell-samples.md).

