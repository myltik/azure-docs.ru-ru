---
title: Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот | Документация Майкрософт
description: Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью интерфейса командной строки Azure.
services: virtual-machines-linux
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2017
ms.locfileid: "23666321"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот

Для компонента "Управляемые диски" доступны два варианта хранилища: уровень [Премиум](../windows/premium-storage.md) (на базе SSD) и уровень [Стандартный](../windows/standard-storage.md) (на базе жестких дисков). Это позволяет с легкостью переключаться между двумя вариантами использования при минимальном времени простоя в зависимости от потребностей производительности. Эта возможность недоступна для неуправляемых дисков. Но вы можете [преобразовать диски в управляемые](convert-unmanaged-to-managed-disks.md), чтобы с легкостью переключаться между двумя вариантами использования.

В этой статье рассматривается преобразование хранилища управляемых дисков с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью интерфейса командной строки Azure. Если вам установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Перед началом работы

* Процесс преобразования требует перезагрузки виртуальной машины, поэтому запланируйте перенос хранилища дисков на предварительно установленный период обслуживания. 
* При использовании неуправляемых дисков сначала [преобразуйте их в управляемые диски](convert-unmanaged-to-managed-disks.md), чтобы воспользоваться приведенными в этой статье сведениями о переключении между двумя вариантами хранения. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Преобразование всех управляемых дисков на виртуальной машине с уровня "Стандартный" до уровня "Премиум" и наоборот

В следующем примере показано, как переключить все диски на виртуальной машине с хранилища уровня "Стандартный" на хранилище уровня "Премиум". Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Преобразование управляемого диска с уровня "Стандартный" до уровня "Премиум" и наоборот

Для среды разработки и тестирования может потребоваться сочетание дисков уровней "Стандартный" и "Премиум", чтобы сократить затраты. Для этого переведите на уровень "Премиум" только диски, для которых требуется более высокая производительность. В следующем примере показано, как переключить диск на виртуальной машине с хранилища уровня "Стандартный" на хранилище уровня "Премиум" и наоборот. Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Дополнительная информация

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).

