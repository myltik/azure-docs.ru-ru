---
title: "Преимущества гибридного использования Azure для Windows Server | Документация Майкрософт"
description: "Узнайте, как воспользоваться преимуществами программы Software Assurance для Windows, чтобы перенести свои локальные лицензии в Azure."
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/02/2017
ms.author: kmouss
ms.openlocfilehash: d47b8ab2cd6391e937fe7f9ba6eded3b89fe2c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Преимущество гибридного использования Azure для Windows Server
Благодаря преимуществам гибридного использования Azure для Windows Server, клиенты, участвующие в программе Software Assurance, могут использовать локальные лицензии Windows Server для запуска виртуальных машин Windows в Azure с меньшими затратами. Вы можете воспользоваться преимуществами гибридного использования Azure для Windows Server, чтобы развернуть новые виртуальные машины из любой поддерживаемой платформы Azure образа Windows Server или пользовательских образов Windows. До тех пор, пока образ не будет поставляться с дополнительным программным обеспечением, например образами SQL Server или сторонними образами из Marketplace. В этой статье приведены инструкции для выполнения развертывания новых виртуальных машин с помощью преимуществ гибридного использования Azure для Windows Server. Дополнительные сведения о лицензировании преимуществ гибридного использования Azure для Windows Server и экономии денежных средств см. [на этой странице](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Образы [HUB] Windows Server, которые были опубликованы для клиентов на условиях соглашения Enterprise в Azure Marketplace, являются устаревшими на 11.09.2017 г. Используйте стандартную версию Windows Server с возможностью экономии денег на портале для предоставляемого преимущества гибридного использования Azure для Windows Server. См. дополнительные сведения в [этой статье](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions).
>

> [!NOTE]
> Преимущество гибридного использования Azure для Windows Server несовместимо с виртуальными машинами, плата с которых взимается за дополнительное программное обеспечение, например образы SQL Server или другие сторонние образы из Marketplace. Вы получите сообщение об ошибке 409, о том что изменение свойства LicenseType не разрешено, если попытаетесь преобразовать виртуальную машину Windows Server, для которой выставляются счета за использование дополнительного программного обеспечения. 
>


> [!NOTE]
> Для классических виртуальных машин развертывание новой виртуальной машины поддерживается только из локальных пользовательских образов. Чтобы воспользоваться преимуществами возможностей, предоставляемых в этой статье, сначала необходимо перенести классическую виртуальную машину в модель Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Способы использования преимуществ гибридного использования Azure для Windows Server
Существует несколько способов использования виртуальных машин Windows с программой преимуществ гибридного использования Azure.

1. Вы можете развернуть виртуальные машины из одного из указанных [образов Windows Server в Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview).
2. Вы можете [передать настраиваемую виртуальную машину](#upload-a-windows-vhd) и [развернуть ее с помощью шаблона Resource Manager](#deploy-a-vm-via-resource-manager) или [Azure PowerShell](#detailed-powershell-deployment-walkthrough).
4. Вы также можете развернуть новый масштабируемый набор виртуальных машин, используя программу преимуществ гибридного использования Azure для Windows Server.

> [!NOTE]
> Преобразование имеющейся виртуальной машины или масштабируемого набора виртуальных машин для использования преимуществ гибридного использования Azure для Windows Server сейчас не поддерживается.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Развертывание виртуальной машины из образа Windows Server Marketplace
Все образы Windows Server, доступные из Azure Marketplace, поддерживают преимущество гибридного использования Azure для Windows Server. Например, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 и Windows Server 2008SP1 и другие. Вы можете использовать эти образы, чтобы развернуть виртуальные машины непосредственно с портала Azure, с помощью шаблонов Resource Manager, Azure PowerShell или других пакетов SDK.

Эти образы можно развернуть непосредственно с помощью портала Azure. Чтобы использовать их в шаблонах Resource Manager и Azure PowerShell, ознакомьтесь со списком образов ниже.

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Выполните действия, указанные в статье [Создание виртуальной машины Windows с помощью PowerShell](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json), чтобы передать LicenseType = Windows_Server. Этот вариант позволяет использовать имеющуюся лицензию Windows Server в Azure.

### <a name="portal"></a>Microsoft Azure
Вы можете воспользоваться инструкциями в статье [Создание виртуальной машины Windows с помощью портала Azure](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) и выбрать вариант использования имеющейся лицензии Windows Server.

## <a name="upload-a-windows-server-vhd"></a>Отправка виртуального жесткого диска Windows Server
Чтобы развернуть виртуальную машину Windows Server в Azure, сначала необходимо создать виртуальный жесткий диск, в котором содержится базовая сборка Windows. Перед отправкой виртуального жесткого диска в Azure его необходимо соответствующим образом подготовить с помощью программы Sysprep. Вы можете [узнать больше о требованиях к VHD и использованию Sysprep](upload-generalized-managed.md) и ознакомиться с разделом [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка ролей сервера в Sysprep). Перед выполнением программы Sysprep выполните архивацию виртуальной машины. 

Чтобы передать подготовленный виртуальный жесткий диск в учетную запись хранения Azure, выполните следующий командлет:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server и Dynamics могут использовать также ваше лицензирование Software Assurance. Еще вам нужно подготовить образ Windows Server. Для этого установите компоненты приложения, предоставьте соответствующие лицензионные ключи и выгрузите образ диска в Azure. Ознакомьтесь с соответствующей документацией, содержащей информацию о запуске Sysprep с помощью вашего приложения, например со статьей [Вопросы по установке SQL Server с помощью SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) или [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Создание эталонного образа SharePoint Server 2016 (Sysprep)).
>
>

Кроме того, вы можете ознакомиться с дополнительной информацией о [передаче VHD в Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account).


## <a name="deploy-a-vm-via-resource-manager-template"></a>Развертывание виртуальной машины с помощью шаблона Resource Manager
В шаблонах Resource Manager нужно указывать дополнительный параметр `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../../resource-group-authoring-templates.md). После загрузки виртуального жесткого диска в Azure необходимо изменить шаблон Resource Manager, чтобы включить в него тип лицензирования как часть поставщика вычислительных ресурсов и развернуть шаблон в обычном режиме.

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Краткое руководство по развертыванию виртуальной машины с помощью PowerShell
При развертывании виртуальной машины Windows Server с помощью PowerShell доступен дополнительный параметр `-LicenseType`. После передачи виртуального жесткого диска в Azure необходимо создать новую виртуальную машину с помощью командлета `New-AzureRmVM` и указать тип лицензирования следующим образом.

Для Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Дополнительные инструкции можно просмотреть в более подробном руководстве [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Проверка наличия льготы на гибридное использование Azure для Windows Server
После развертывания виртуальной машины с помощью шаблона Resource Manager, PowerShell или портала, можно проверить тип лицензии с помощью командлета `Get-AzureRmVM` следующим образом.

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Вы должны увидеть результат, аналогичный приведенному ниже примеру для Windows Server.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Эти выходные данные отличаются от полученных при развертывании виртуальной машины без лицензии на программу преимуществ гибридного использования Azure.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Список всех преимуществ гибридного использования Azure для виртуальных машин Windows Server в подписке

Чтобы просмотреть и подсчитать все виртуальные машины, развернутые с помощью преимуществ гибридного использования Azure для Windows Server, выполните следующую команду из своей подписки:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Развертывание нового масштабируемого набора виртуальных машин с помощью программы преимуществ гибридного использования Azure для Windows Server
В шаблонах Resource Manager для масштабируемого набора виртуальных машин нужно указывать дополнительный параметр `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../../resource-group-authoring-templates.md). Измените шаблон Resource Manager, чтобы включить свойство LicenseType как часть VirtualMachineProfile для масштабируемого набора. Затем разверните шаблон в обычном режиме (см. пример ниже с использованием образа Windows Server 2016):


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Вы также можете [создать и развернуть масштабируемый набор виртуальных машин](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) и задать свойство LicenseType.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статье [Экономьте до 40 % на виртуальных машинах Windows Server благодаря программе преимуществ гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Узнайте больше об [использовании шаблонов Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Узнайте больше о том, как [программа преимуществ гибридного использования Azure для Windows Server и Azure Site Recovery делает перенос приложений в Azure еще более экономичным](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

Ознакомьтесь с [часто задаваемыми вопросами](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/).
