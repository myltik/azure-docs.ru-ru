---
title: "Создание пользовательских образов виртуальных машин с помощью Azure PowerShell | Документация Майкрософт"
description: "Руководство по созданию пользовательского образа виртуальной машины с помощью Azure PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 773b37ec8f775d68f1faca0d252f3064c7de0317
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Создание пользовательского образа виртуальной машины Azure с помощью PowerShell

Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. В рамках этого руководства вы создадите собственный пользовательский образ виртуальной машины Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка виртуальной машины к использованию с помощью Sysprep
> * Создание пользовательского образа
> * Создание виртуальной машины из пользовательского образа
> * Получение списка всех образов в подписке
> * Удаление образа

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Перед началом работы

Ниже подробно описано, как преобразовать существующую виртуальную машину в многократно используемый пользовательский образ, на основе которого можно создавать экземпляры виртуальной машины.

Для выполнения примера в этом руководстве требуется виртуальная машина. Этот [пример сценария](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) позволяет создать ее при необходимости. При работе с примером по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.

## <a name="prepare-vm"></a>Подготовка виртуальной машины

Чтобы создать образ виртуальной машины, нужно подготовить ее к использованию, отозвав и пометив исходную виртуальную машину как универсальную в Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).


1. Подключитесь к виртуальной машине.
2. Откройте окно командной строки с правами администратора. Измените каталог на *%windir%\system32\sysprep* и запустите файл *sysprep.exe*.
3. В диалоговом окне **Программа подготовки системы** выберите *Переход в окно приветствия системы (OOBE)* и убедитесь, что установлен флажок *Подготовка к использованию*.
4. В разделе **Параметры завершения работы** выберите *Завершение работы* и нажмите кнопку **ОК**.
5. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. **Не перезапускайте виртуальную машину.**

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Отмена выделения и пометка виртуальной машины как обобщенной

Чтобы создать образ виртуальной машины, необходимо отменить ее выделение и пометить ее как универсальную в Azure.

Отмените выделение виртуальной машины с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Теперь задайте для виртуальной машины состояние `-Generalized`, выполнив командлет [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Создание образа

Теперь можно создать образ виртуальной машины с помощью командлетов [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) и [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). В следующем примере создается образ *myImage* из виртуальной машины *myVM*.

Получите виртуальную машину. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Создайте конфигурацию образа.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Создайте образ.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Создание виртуальных машин из образа

Теперь, когда образ готов, из него можно создать одну или несколько виртуальных машин. Создание виртуальной машины из образа очень похоже на создание виртуальной машины с помощью образа Marketplace. При использовании образа Marketplace требуются сведения об образе, его поставщике, предложении, номере SKU и версии. В случае пользовательского образа необходимо просто указать идентификатор его ресурса. 

В следующем сценарии создается переменная *$image* для сохранения сведений о пользовательском образе с помощью командлета [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage), затем выполняется командлет [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) и указывается идентификатор с помощью только что созданной переменной *$image*. 

Этот сценарий создает виртуальную машину *myVMfromImage* из пользовательского образа в новой группе ресурсов *myResourceGroupFromImage* в расположении *Западная часть США*.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Управление образами 

Ниже приведены некоторые примеры распространенных задач управления образами, а также способы их выполнения с помощью PowerShell.

Вывод списка всех образов по имени.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Удаление образа. В этом примере из *myResourceGroup* удаляется образ с именем *myOldImage*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы создали пользовательский образ виртуальной машины. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка виртуальной машины к использованию с помощью Sysprep
> * Создание пользовательского образа
> * Создание виртуальной машины из пользовательского образа
> * Получение списка всех образов в подписке
> * Удаление образа

Перейдите к следующему руководству, чтобы узнать о высокодоступных виртуальных машинах.

> [!div class="nextstepaction"]
> [Создание высокодоступных виртуальных машин](tutorial-availability-sets.md)




