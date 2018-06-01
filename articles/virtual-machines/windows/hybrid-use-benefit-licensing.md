---
title: Преимущества гибридного использования Azure для Windows Server | Документация Майкрософт
description: Узнайте, как воспользоваться преимуществами программы Software Assurance для Windows, чтобы перенести свои локальные лицензии в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071962"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Преимущество гибридного использования Azure для Windows Server
Благодаря преимуществам гибридного использования Azure для Windows Server, клиенты, участвующие в программе Software Assurance, могут использовать локальные лицензии Windows Server для запуска виртуальных машин Windows в Azure с меньшими затратами. С помощью Преимущества гибридного использования Azure также можно развертывать новые виртуальные машины с ОС Windows. В этой статье описывается, как выполнить развертывание новых виртуальных машин с помощью преимуществ гибридного использования Azure для Windows Server, а также как обновить существующие запущенные виртуальные машины. Дополнительные сведения о лицензировании преимуществ гибридного использования Azure для Windows Server и экономии денежных средств см. [на этой странице](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> В каждой лицензии, рассчитанной на использование 2 процессоров, или каждом наборе лицензий, рассчитанном на предоставление 16 ядер, может предусматриваться либо два экземпляра по 8 ядер, либо один экземпляр на 16 ядер. Преимущество гибридного использования Azure для лицензий на выпуск Standard можно использовать только один раз: либо локально, либо в среде Azure. Преимущества для выпуска Datacenter можно использовать и локально, и в среде Azure.
>

> [!Important]
> Преимущество гибридного использования Azure для Windows Server теперь можно использовать с любыми виртуальными машинами, работающими под управлением Windows Server, во всех регионах, включая виртуальные машины с дополнительным программным обеспечением, таким как SQL Server или стороннее ПО из магазина. 
>

> [!NOTE]
> Для классических виртуальных машин развертывание новой виртуальной машины поддерживается только из локальных пользовательских образов. Чтобы воспользоваться преимуществами возможностей, предоставляемых в этой статье, сначала необходимо перенести классическую виртуальную машину в модель Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Способы использования преимуществ гибридного использования Azure для Windows Server
Существует несколько способов использования виртуальных машин Windows с программой преимуществ гибридного использования Azure.

1. Вы можете развернуть виртуальные машины из одного из указанных [образов Windows Server в Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview).
2. Вы можете передать настраиваемую виртуальную машину и развернуть ее с помощью шаблона Resource Manager или Azure PowerShell.
3. Вы можете переключать и преобразовывать существующую виртуальную машину для Windows Server так, чтобы использовать ее с программой "Преимущество гибридного использования Azure" или с моделью оплаты по требованию .
4. Вы также можете применить Преимущество гибридного использования Azure для Windows Server к масштабируемому набору виртуальных машин.


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Создание виртуальной машины с помощью Преимущества гибридного использования Azure для Windows Server
Все образы на основе Windows Server поддерживают Преимущество гибридного использования Azure для Windows Server. Вы можете использовать образы, предоставляемые платформой Azure, или передать собственные образы Windows Server. 

### <a name="portal"></a>Microsoft Azure
Чтобы создать виртуальную машину с помощью Преимущества гибридного использования Azure для Windows Server, воспользуйтесь переключателем в разделе "Сэкономьте деньги".

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>Интерфейс командной строки
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Шаблон
В шаблонах Resource Manager нужно указывать дополнительный параметр `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов Azure Resource Manager](../../resource-group-authoring-templates.md).
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Преобразование существующей виртуальной машины с поддержкой программы "Преимущество гибридного использования Azure" для Windows Server
Если у вас есть виртуальная машина, которую требуется преобразовать для использования с программой "Преимущество гибридного использования Azure" для Windows Server, вы можете обновить тип лицензии виртуальной машины следующим образом:

### <a name="portal"></a>Microsoft Azure
В колонке виртуальной машины на портале можно обновить виртуальную машину для использования с программой "Преимущество гибридного использования Azure", выбрав параметр "Конфигурация" и переключив параметр "Преимущество гибридного использования Azure".

### <a name="powershell"></a>PowerShell
- Преобразование существующих виртуальных машин Windows Server в виртуальные машины с поддержкой Преимущества гибридного использования Azure для Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Перевод виртуальных машин Windows Server с поддержкой преимущества обратно на модель оплаты по мере использования

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Интерфейс командной строки
- Преобразование существующих виртуальных машин Windows Server в виртуальные машины с поддержкой Преимущества гибридного использования Azure для Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Проверка наличия льготы на лицензирование у виртуальной машины
После развертывания виртуальной машины с помощью шаблона Resource Manager, PowerShell или портала можно проверить наличие преимущества одним из описанных ниже методов.

### <a name="portal"></a>Microsoft Azure
В колонке виртуальной машины на портале выберите вкладку "Конфигурация", чтобы увидеть переключатель Преимущества гибридного использования Azure для Windows Server.

### <a name="powershell"></a>PowerShell
В приведенном ниже примере показан тип лицензии для одной виртуальной машины.
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Выходные данные:
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

### <a name="cli"></a>Интерфейс командной строки
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Вывод списка всех виртуальных машин с Преимуществом гибридного использования Azure для Windows Server в подписке
Чтобы просмотреть и подсчитать все виртуальные машины, развернутые с помощью преимуществ гибридного использования Azure для Windows Server, выполните следующую команду из своей подписки:

### <a name="portal"></a>Microsoft Azure
В колонке ресурсов виртуальных машин или масштабируемых наборов виртуальных машин можно просмотреть список всех виртуальных машин и их тип лицензирования. Для этого нужно включить в таблицу столбец "Преимущество гибридного использования Azure". Виртуальная машина может иметь состояние "Включено", "Не включено" или "Не поддерживается".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Интерфейс командной строки
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Развертывание масштабируемого набора виртуальных машин с помощью Преимущества гибридного использования Azure для Windows Server
В шаблонах Resource Manager для масштабируемого набора виртуальных машин нужно указывать дополнительный параметр `licenseType` в свойстве VirtualMachineProfile. Это можно сделать во время создания или обновления масштабируемого набора с помощью шаблона ARM, PowerShell, Azure CLI или REST.

В следующем примере используется шаблон ARM с образом Windows Server 2016 Datacenter:
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
Сведения о дополнительных возможностях изменения масштабируемого набора см. в статье [Изменение масштабируемого набора виртуальных машин](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md).

## <a name="next-steps"></a>Дополнительная информация
- Прочитайте больше о том, [как экономить при использовании программы "Преимущество гибридного использования Azure"](https://azure.microsoft.com/pricing/hybrid-use-benefit/).
- Ознакомьтесь с [часто задаваемыми вопросами о Преимуществе гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/).
- См. дополнительные сведения о [программе "Преимущество гибридного использования Azure" для Windows Server](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit).
- Узнайте больше о том, как [программа "Преимущество гибридного использования Azure" для Windows Server и Azure Site Recovery делает перенос приложений в Azure еще более экономичным](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).
- Узнайте больше о том, как [развернуть Windows 10 в Azure с правами на мультитенантное размещение](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment).
- Узнайте больше об [использовании шаблонов Resource Manager](../../azure-resource-manager/resource-group-overview.md).
