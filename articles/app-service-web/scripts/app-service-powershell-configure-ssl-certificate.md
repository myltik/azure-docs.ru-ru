---
title: "Пример сценария Azure PowerShell. Привязка пользовательского SSL-сертификата к веб-приложению | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для привязки пользовательского SSL-сертификата к веб-приложению."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 9b8d26edbdd983eed521e4e1737077bd639532d9
ms.lasthandoff: 03/10/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Привязка пользовательского SSL-сертификата к веб-приложению

Этот сценарий создает в службе приложений веб-приложение со связанными ресурсами, а затем привязывает к нему SSL-сертификат имени личного домена. 

Перед выполнением этого сценария проверьте следующее.

- Подключение к Azure установлено с помощью команды `az login`.
- У вас есть доступ к странице конфигурации DNS вашего регистратора доменных имен.
- У вас есть допустимый PFX-файл и пароль для SSL-сертификата, который требуется передать и привязать.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Привязка SSL-сертификата к веб-приложению")]

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
| [Set-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermappserviceplan) | Обновляет план службы приложений для изменения ценовой категории. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Изменяет конфигурацию веб-приложения. |
| [New-AzureRmWebAppSSLBinding](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebappsslbinding) | Создает привязку SSL-сертификата к веб-приложению. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../app-service-powershell-samples.md).

