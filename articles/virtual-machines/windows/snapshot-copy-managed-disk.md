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
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: c5f4c7224e04b601d7d3fe4da7d8f5f0c02c7039
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
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

Установите последнюю версию модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](/powershell/azure/overview).


1. Задайте некоторые параметры. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Скопируйте диск VHD.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Создайте конфигурации моментального снимка. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Создайте моментальный снимок.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Если вы планируете использовать моментальный снимок, чтобы создать управляемый диск и подключить его к виртуальной машине, которая должна быть высокопроизводительной, используйте параметр `-AccountType Premium_LRS` в команде New-AzureRmSnapshot. Параметр создает моментальный снимок таким образом, чтобы он хранился в качестве управляемого диска уровня "Премиум". Управляемые диски уровня "Премиум" дороже, чем управляемые диски уровня "Стандартный". Поэтому, прежде чем использовать этот параметр, убедитесь, что вам действительно необходим управляемый диск уровня "Премиум".

## <a name="next-steps"></a>Дополнительная информация

Создайте виртуальную машину из моментального снимка, преобразовав его в управляемый диск, а затем подключив этот диск как диск ОС. Чтобы получить дополнительные сведения, см. пример [создания виртуальной машины из моментального снимка](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
