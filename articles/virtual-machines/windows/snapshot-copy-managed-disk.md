---
title: Создание моментального снимка виртуального жесткого диска в Azure | Документация Майкрософт
description: Узнайте, как создать копию виртуальной машины Azure в качестве резервной копии или для устранения неполадок.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 7d45fd749fea4036d944d740541d8b8607553835
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658161"
---
# <a name="create-a-snapshot"></a>Создание моментального снимка

Создание моментального снимка операционной системы или виртуального жесткого диска данных для резервного копирования или устранения неполадок с виртуальной машиной. Моментальный снимок — это полная копия виртуального жесткого диска, доступная только для чтения. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Использование портала Azure для создания моментальных снимков 

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левом верхнем углу щелкните **Создать ресурс** и найдите **моментальный снимок**.
3. В колонке "Моментальный снимок" щелкните **Создать**.
4. Заполните поле **Имя** для моментального снимка.
5. Выберите существующую [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#resource-groups) или укажите имя, чтобы создать новую. 
6. Выберите расположение центра обработки данных Azure.  
7. В поле **Исходный диск** выберите управляемый диск, моментальный снимок которого необходимо создать.
8. Выберите **тип учетной записи**, которая будет использоваться для хранения моментального снимка. Мы рекомендуем тип **Standard_LRS**, если вам не требуется хранить моментальный снимок на высокопроизводительном диске.
9. Нажмите кнопку **Создать**.

## <a name="use-powershell-to-take-a-snapshot"></a>Использование PowerShell для создания моментальных снимков

Ниже показано, как скопировать диск VHD, создать конфигурации моментального снимка и собственно моментальный снимок диска с помощью командлета [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Прежде чем начать, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Для работы с этой статьей требуется модуль AzureRM 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

Задайте некоторые параметры. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Получите виртуальную машину.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Создайте конфигурацию моментального снимка. В этом примере мы собираемся создать моментальный снимок диск ОС.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Перед сохранением моментального снимка в хранилище, избыточном в пределах зоны, его необходимо создать в регионе, который поддерживает [зоны доступности](../../availability-zones/az-overview.md) и в котором включен параметр `-SkuName Standard_ZRS`.   

   
Создайте моментальный снимок.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Дополнительная информация

Создайте виртуальную машину из моментального снимка, преобразовав его в управляемый диск, а затем подключив этот диск как диск ОС. Чтобы получить дополнительные сведения, см. пример [создания виртуальной машины из моментального снимка](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
