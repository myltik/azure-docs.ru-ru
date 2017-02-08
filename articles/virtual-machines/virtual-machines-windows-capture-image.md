---
title: "Запись образа виртуальной машины на основе универсальной виртуальной машины Azure | Документация Майкрософт"
description: "Сведения о записи образа виртуальной машины на основе универсальной виртуальной машины Azure, созданной с помощью модели развертывания Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: dc0753faefa59eef42046e0297a58276db086d18


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Запись образа виртуальной машины на основе универсальной виртуальной машины Azure
В этой статье показано, как использовать Azure PowerShell для создания образа универсальной виртуальной машины Azure. Такой образ можно затем использовать для создания другой виртуальной машины. Этот образ включает в себя диск операционной системы и другие диски данных, присоединенные к виртуальной машине. Образ не включает ресурсы виртуальной сети, поэтому их придется настроить при создании виртуальной машины. 

## <a name="prerequisites"></a>Предварительные требования
* У вас должна быть [подготовленная виртуальная машина](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). При подготовке виртуальной машины удаляются все сведения о вашей учетной записи и выполняется настройка ВМ для использования в качестве образа.
* У вас должна быть установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы еще не установили PowerShell, см. статью [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell
1. Откройте Azure PowerShell и войдите в свою учетную запись Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Откроется всплывающее окно для ввода данных учетной записи Azure.
2. Получите идентификаторы доступных вам подписок.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Укажите соответствующую подписку с помощью идентификатора.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Отмена распределения для виртуальной машины и установка состояния "универсальный"
1. Отмените распределение ресурсов для виртуальной машины.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Состояние* виртуальной машины на портале Azure изменится с **Остановлено** на **Остановлено (освобождено)**.
2. Теперь задайте для виртуальной машины состояние **Generalized**(Универсальная). 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Проверьте состояние виртуальной машины. В разделе **OSState/generalized** для этой виртуальной машины параметр **DisplayStatus** должен иметь значение **VM generalized**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Создание образа
1. С помощью этой команды скопируйте образ виртуальной машины в целевой контейнер хранилища. Образ будет создан в той же учетной записи хранения, что и исходная виртуальная машина. Параметр `-Path` сохраняет локальную копию шаблона JSON. Параметр `-DestinationContainerName` содержит имя контейнера для хранения образов. Если такой контейнер не существует, он будет создан автоматически.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    URL-адрес образа можно получить из шаблона файла JSON. Перейдите к разделу **resources** > **storageProfile** > **osDisk** > **image** > **uri**, чтобы получить полный путь к образу. URL-адрес образа выглядит так: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    Также вы также можете проверить URI на портале. Образ копируется в контейнер **system** в вашей учетной записи хранения. 

## <a name="next-steps"></a>Дальнейшие действия
* Теперь можно [создать виртуальную машины из образа](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Dec16_HO2-->


