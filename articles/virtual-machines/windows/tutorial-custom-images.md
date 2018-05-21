---
title: Руководство. Создание пользовательских образов виртуальных машин с помощью Azure PowerShell | Документация Майкрософт
description: В этом руководстве описано, как с помощью Azure PowerShell создать пользовательский образ виртуальной машины Linux в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a449c1f9781ffc86de4786eaab3cb83999b86a72
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Руководство. Создание пользовательского образа виртуальной машины Azure с помощью Azure PowerShell

Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. В рамках этого руководства вы создадите собственный пользовательский образ виртуальной машины Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка виртуальной машины к использованию с помощью Sysprep
> * Создание пользовательского образа
> * Создание виртуальной машины из пользовательского образа
> * Получение списка всех образов в подписке
> * удалять образ.

## <a name="before-you-begin"></a>Перед началом работы

Ниже подробно описано, как преобразовать существующую виртуальную машину в многократно используемый пользовательский образ, на основе которого можно создавать экземпляры виртуальной машины.

Для выполнения примера в этом руководстве требуется виртуальная машина. Этот [пример сценария](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) позволяет создать ее при необходимости. При работе с примером по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль AzureRM 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="prepare-vm"></a>Подготовка виртуальной машины

Чтобы создать образ виртуальной машины, нужно подготовить ее к использованию, отозвав и пометив исходную виртуальную машину как универсальную в Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).


1. Подключитесь к виртуальной машине.
2. Откройте окно командной строки с правами администратора. Измените каталог на *%windir%\system32\sysprep* и запустите файл *sysprep.exe*.
3. В диалоговом окне **Программа подготовки системы** выберите *Переход в окно приветствия системы (OOBE)* и убедитесь, что установлен флажок *Подготовка к использованию*.
4. В разделе **Параметры завершения работы** выберите *Завершение работы* и нажмите кнопку **ОК**.
5. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. **Не перезапускайте виртуальную машину.**

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Отмена выделения и пометка виртуальной машины как обобщенной

Чтобы создать образ виртуальной машины, необходимо отменить ее выделение и пометить ее как универсальную в Azure.

Отмените выделение виртуальной машины с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Теперь задайте для виртуальной машины состояние `-Generalized`, выполнив командлет [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Создание образа

Теперь можно создать образ виртуальной машины с помощью командлетов [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) и [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). В следующем примере создается образ *myImage* из виртуальной машины *myVM*.

Получите виртуальную машину. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Создайте конфигурацию образа.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Создайте образ.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Создание виртуальных машин из образа

Теперь, когда образ готов, из него можно создать одну или несколько виртуальных машин. Создание виртуальной машины из образа похоже на создание виртуальной машины с помощью образа Marketplace. При использовании образа Marketplace требуется указать сведения об образе, его поставщике, предложении, номере SKU и версии. С помощью упрощенного набора параметров для командлета [New AzureRMVM]() укажите имя настраиваемого образа, поскольку он находится в той же группе ресурсов. 

В этом примере создается виртуальная машина *myVMfromImage* из образа *myImage* в группе ресурсов *myResourceGroup*.


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Управление образами 

Ниже приведены некоторые примеры распространенных задач управления образами, а также способы их выполнения с помощью PowerShell.

Вывод списка всех образов по имени.

```azurepowershell-interactive
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Удаление образа. В этом примере из *myResourceGroup* удаляется образ с именем *myOldImage*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

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



