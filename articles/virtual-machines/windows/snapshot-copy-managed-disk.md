---
title: "Создание копии управляемого диска Azure для архивации | Документация Майкрософт"
description: "Узнайте, как создать копию управляемого диска Azure, используемого для архивации или устранения неполадок с дисками."
documentationcenter: 
author: cwatsonMSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: bb913050fd3388d4632e6f75b36415006f370cbd
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Создание копии виртуального жесткого диска, хранящегося в виде управляемого диска Azure, с помощью управляемых моментальных снимков
Создайте моментальный снимок управляемого диска для архивации или создайте управляемый диск на основе моментального снимка и подключите его к тестовой виртуальной машине для устранения неполадок. Управляемый моментальный снимок — это полная копия управляемого диска виртуальной машины на определенный момент времени. Он создает копию виртуального жесткого диска только для чтения и по умолчанию сохраняет ее в качестве управляемого диска уровня "Стандартный". Дополнительные сведения об Управляемых дисках Azure см. в [обзоре Управляемых дисков Azure](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Дополнительные сведения о ценах см. на странице [с ценами на службу хранилища Azure](https://azure.microsoft.com/pricing/details/managed-disks/). 

## <a name="before-you-begin"></a>Перед началом работы
Если вы используете PowerShell, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](/powershell/azure/overview).

## <a name="copy-the-vhd-with-a-snapshot"></a>Копирование VHD с использованием моментального снимка
Чтобы создать моментальный снимок управляемого диска, используйте портал Azure или PowerShell.

### <a name="use-azure-portal-to-take-a-snapshot"></a>Использование портала Azure для создания моментальных снимков 

1. Войдите на [портал Azure](https://portal.azure.com).
2. В левом верхнем углу щелкните **Создать** и найдите **моментальный снимок**.
3. В колонке "Моментальный снимок" щелкните **Создать**.
4. Заполните поле **Имя** для моментального снимка.
5. Выберите существующую [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#resource-groups) или укажите имя, чтобы создать новую. 
6. Выберите расположение центра обработки данных Azure.  
7. В поле **Исходный диск** выберите управляемый диск, моментальный снимок которого необходимо создать.
8. Выберите **тип учетной записи**, которая будет использоваться для хранения моментального снимка. Мы рекомендуем тип **Standard_LRS**, если вам не требуется хранить моментальный снимок на высокопроизводительном диске.
9. Щелкните **Создать**.

### <a name="use-powershell-to-take-a-snapshot"></a>Использование PowerShell для создания моментальных снимков
Ниже показано, как скопировать диск VHD, создать конфигурации моментального снимка и создать моментальный снимок диска с помощью командлета New-AzureRmSnapshot<!--Add link to cmdlet when available-->. 

1. Задайте некоторые параметры. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  Замените значения следующих параметров:
  -  myResourceGroup — группой ресурсов виртуальной машины;
  -  southeastasia — географическим расположением, в котором должен храниться управляемый моментальный снимок; <!---How do you look these up? -->
  -  ContosoMD_datadisk1 — именем диска VHD, который необходимо скопировать;
  -  ContosoMD_datadisk1_snapshot1 — именем, которое необходимо использовать для нового моментального снимка.

2. Скопируйте диск VHD.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Создайте конфигурации моментального снимка. 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Создайте моментальный снимок.

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Если вы планируете использовать моментальный снимок, чтобы создать управляемый диск и подключить его к виртуальной машине, которая должна быть высокопроизводительной, используйте параметр `-AccountType Premium_LRS` в команде New-AzureRmSnapshot. Параметр создает моментальный снимок таким образом, чтобы он хранился в качестве управляемого диска уровня "Премиум". Управляемые диски уровня "Премиум" дороже, чем управляемые диски уровня "Стандартный". Поэтому, прежде чем использовать этот параметр, убедитесь, что вам действительно необходим управляемый диск уровня "Премиум".



