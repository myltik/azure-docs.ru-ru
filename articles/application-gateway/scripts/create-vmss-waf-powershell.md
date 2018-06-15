---
title: Пример скрипта Azure PowerShell. Ограничение веб-трафика | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Создание шлюза приложения с брандмауэром веб-приложения и масштабируемым набором виртуальных машин, использующих правила OWASP для ограничения трафика.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d6f8083e0070435e6ad8bb0dc0ff520b58c7eead
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33202292"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>Ограничение веб-трафика с помощью Azure PowerShell

Этот скрипт создает шлюз приложений с брандмауэром веб-приложения, в котором используется масштабируемый набор виртуальных машин для внутренних серверов. Брандмауэр веб-приложения ограничивает веб-трафик на основе правил OWASP. Выполнив скрипт, вы можете проверить шлюз приложений с помощью его общедоступного IP-адреса.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss-waf.ps1 "Create application gateway with WAF")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, шлюз приложений и все связанные ресурсы:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Создает конфигурацию подсети. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Создает виртуальную сеть с конфигурациями подсетей. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает общедоступный IP-адрес для шлюза приложений. |
| [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) | Создает конфигурацию, которая связывает подсеть со шлюзом приложений. |
| [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) | Создает конфигурацию, которая назначает общедоступный IP-адрес шлюзу приложений. |
| [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) | Назначает порт для доступа к шлюзу приложений. |
| [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) | Создает серверный пул для шлюза приложения. |
| [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) | Настраивает параметры для серверного пула. |
| [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) | Создает прослушиватель. |
| [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) | Создает правило маршрутизации. |
| [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) | Определяет уровень и производительность для шлюза приложения. |
| [New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewaywebapplicationfirewallconfiguration) | Настраивает конфигурацию брандмауэра веб-приложения. |
| [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) | Создание шлюза приложений. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Создает профиль хранилища для масштабируемого набора. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Определяет операционную систему для масштабируемого набора. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Определяет сетевой интерфейс для масштабируемого набора. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm) | Создает масштабируемый набор виртуальных машин. |
| [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Создание учетной записи хранения. |
| [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) | Настраивает средства диагностики для записи данных. |
| [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Получает общедоступный IP-адрес шлюза приложений. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. | 
## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Другие примеры скриптов PowerShell можно найти в [документации по шлюзу приложений Azure](../powershell-samples.md).
