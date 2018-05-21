---
title: Руководство. Установка приложений на виртуальной машине Windows в Azure | Документация Майкрософт
description: В этом руководстве описано, как использовать расширение пользовательских скриптов для выполнения скриптов и развертывания приложений на виртуальных машинах Windows в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 51a66d524e72968d95f3415b890567015165063a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Руководство. Развертывание приложений на виртуальной машине Windows в Azure с помощью расширения пользовательских скриптов

Для быстрой и согласованной настройки виртуальных машин, как правило, применяются средства автоматизации. Часто для настройки виртуальной машины Windows применяется [расширение настраиваемых сценариев для Windows](extensions-customscript.md). Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов на установки IIS
> * Создание виртуальной машины, которая использует расширение пользовательских скриптов
> * Просмотр выполнения сайта IIS после применения расширения

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.


## <a name="custom-script-extension-overview"></a>Общие сведения о расширении настраиваемых сценариев
Расширение настраиваемых сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения.

Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager, а также его можно запустить с помощью интерфейса командной строки Azure, PowerShell, портала Azure или API REST виртуальной машины Azure.

Расширение настраиваемых сценариев можно использовать для виртуальных машин как в Windows, так и в Linux.


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Сначала укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Теперь вы можете создать виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVM* в расположении *EastUS*. Если они еще не существуют, создаются поддерживающие сетевые ресурсы и группа доступности *myResourceGroupAutomate*. Чтобы разрешить веб-трафик, командлет также открывает порт *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Создание этих ресурсов и виртуальной машины может занять несколько минут.


## <a name="automate-iis-install"></a>Автоматизация установки IIS
Воспользуйтесь командлетом [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension), чтобы установить расширение настраиваемых сценариев. Это расширение запускает `powershell Add-WindowsFeature Web-Server` для установки веб-сервера IIS, а затем обновляет страницу *Default.htm* для отображения имени узла виртуальной машины.

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Проверка веб-сайта
Получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIPAddress*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

После этого можно ввести общедоступный IP-адрес в веб-браузер. Отображается веб-сайт, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик, как показано в следующем примере:

![Выполнение веб-сайта IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы автоматизировали установку IIS на виртуальной машине. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов на установки IIS
> * Создание виртуальной машины, которая использует расширение пользовательских скриптов
> * Просмотр выполнения сайта IIS после применения расширения

Перейдите к следующему руководству, чтобы научиться создавать пользовательские образы виртуальных машин.

> [!div class="nextstepaction"]
> [Создание образа настраиваемой виртуальной машины](./tutorial-custom-images.md)
