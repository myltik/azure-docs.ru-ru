---
title: "Создание виртуальной Машины Windows с помощью упрощенного командлет New-AzureRMVM в оболочке облако Azure | Документы Microsoft"
description: "Быстро научиться создавать виртуальные машины Windows с помощью упрощенный командлета New-AzureRMVM в оболочке облако Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Создание виртуальной машины Windows с командлетом New-AzureRMVM упрощенный в оболочке облака 

[New AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) командлет добавлен упрощенный набор параметров для создания новой виртуальной Машины с помощью PowerShell. В этом разделе показано, как использовать PowerShell в оболочку облако Azure, с последней версией командлет New-AzureVM предустановленными, для создания новой виртуальной Машины. Мы будем использовать набор параметров упрощенный, автоматически создает все необходимые ресурсы, с помощью смарт-значения по умолчанию. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell версии 5.1.1 или более поздней. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-the-vm"></a>Создание виртуальной машины

Можно использовать [New AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) для создания виртуальной Машины со смарт-значениями по умолчанию, которые включают в себя использование образа Windows Server 2016 Datacenter из Azure Marketplace. Можно использовать New-AzureRMVM с только что **-имя** параметра и он будет использовать это значение для всех имен ресурсов. В этом примере в качестве параметра **-Name** задается значение *myVM*. 

Убедитесь, что **PowerShell** выбран в Cloud Shell, и введите:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Вам будет предложено создать имя пользователя и пароль для виртуальной машины, которые будут использоваться при подключении к виртуальной машине далее в этой статье. Пароль должен содержать от 12 до 123 символов и включать по меньшей мере три из следующих символов: одна строчная буква, одна прописная буква, одна цифра и один специальный символ.

Создание виртуальной машины и связанных с ней ресурсов занимает минуту. По завершении вы увидите все ресурсы, которые были созданы с помощью командлета [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

После завершения развертывания создайте подключение к удаленному рабочему столу виртуальной машины.

Получите общедоступный IP-адрес виртуальной машины, выполнив команду [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Запишите этот IP-адрес.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

На локальном компьютере, откройте командную строку и использовать **mstsc** команду, чтобы начать сеанс удаленного рабочего стола с вашей новой виртуальной Машины. Замените &lt;publicIPAddress&gt; IP-адресом виртуальной машины. При появлении запроса введите имя пользователя и пароль, предоставленные вашей виртуальной машине при создании.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Укажите имена различных ресурсов

Можно также предоставить понятные имена для ресурсов и по-прежнему использовать создается автоматически. Ниже приведен пример, где иметь именованные несколько ресурсов для новой виртуальной Машины, включая новую группу ресурсов.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

При работе с этой статьей вы развернули простую виртуальную машину с помощью командлета New-AzVM, а затем подключились к ней с помощью протокола удаленного рабочего стола. Дополнительные сведения о виртуальных машинах Azure см. в руководстве по работе с виртуальными машинами Windows.

> [!div class="nextstepaction"]
> [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](./tutorial-manage-vm.md)
