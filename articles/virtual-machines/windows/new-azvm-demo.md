---
title: "Создание виртуальной машины Windows с помощью командлета New-AzVM в Azure Cloud Shell | Документация Майкрософт"
description: "Быстро научитесь создавать виртуальные машины Windows с помощью командлета New-AzVM в Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Создание виртуальной машины Windows с помощью командлета New-AzVM (предварительная версия) в Cloud Shell 

Командлет New-AzVM (предварительная версия) представляет упрощенный способ создания новой виртуальной машины с помощью PowerShell. В этом руководстве подробно описывается использование PowerShell в Azure Cloud Shell с предустановленным командлетом New-AzVM для создания новой виртуальной машины Azure под управлением Windows Server 2016. После завершения развертывания мы подключимся к серверу с помощью протокола удаленного рабочего стола.  

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Создание виртуальной машины

Можно использовать командлет **New-AzVM** для создания виртуальной машины с интеллектуальными значениями по умолчанию, включая использование образа Windows Server 2016 Datacenter из Azure Marketplace. Можно также использовать только командлет New-AzVM. Он будет применять значения по умолчанию для имен ресурсов. В этом примере в качестве параметра **-Name** задается значение *myVM*. Командлет создает все необходимые ресурсы, используя *myVM* в качестве префикса имени ресурса. 

Убедитесь, что **PowerShell** выбран в Cloud Shell, и введите:

```azurepowershell-interactive
New-AzVm -Name myVM
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

На локальном компьютере откройте командную строку и введите команду **mstsc** для запуска сеанса удаленного рабочего стола с новой виртуальной машиной. Замените &lt;publicIPAddress&gt; IP-адресом виртуальной машины. При появлении запроса введите имя пользователя и пароль, предоставленные вашей виртуальной машине при создании.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

При работе с этой статьей вы развернули простую виртуальную машину с помощью командлета New-AzVM, а затем подключились к ней с помощью протокола удаленного рабочего стола. Дополнительные сведения о виртуальных машинах Azure см. в руководстве по работе с виртуальными машинами Windows.

> [!div class="nextstepaction"]
> [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](./tutorial-manage-vm.md)
