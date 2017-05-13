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
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ab19073735816ebb32a9840ec03b31b358ccb565
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Создание пользовательского образа виртуальной машины Azure с помощью PowerShell

В этом руководстве вы узнаете, как определить пользовательский образ виртуальной машины Azure. Пользовательские образы позволяют создавать виртуальные машины с помощью уже настроенного образа. Пользовательские образы можно использовать для начальной загрузки двоичных файлов и приложений, конфигураций приложений, определений дисков данных виртуальных машин и других конфигураций операционной системы. При создании пользовательского образа в него добавляется настраиваемая виртуальная машина со всеми подключенными дисками.

Для работы с этим руководством можно использовать последнюю версию модуля [Azure PowerShell](/powershell/azure/overview).

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
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

Теперь задайте для виртуальной машины состояние `-Generalized`, выполнив командлет [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


## <a name="create-the-image"></a>Создание образа

Теперь можно создать образ виртуальной машины с помощью командлетов [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) и [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). В следующем примере создается образ *myImage* из виртуальной машины *myVM*.

Получите виртуальную машину. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

Создайте конфигурацию образа.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Создайте образ.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
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
    -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы научились создавать пользовательские образы виртуальных машин. Перейдите к следующему руководству, чтобы узнать о высокодоступных виртуальных машинах.

[Создание высокодоступных виртуальных машин](tutorial-availability-sets.md)




