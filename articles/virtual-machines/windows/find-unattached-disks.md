---
title: Поиск и удаление неподключенных управляемых и неуправляемых дисков Azure | Документация Майкрософт
description: Сведения о том, как найти и удалить неподключенные управляемые и неуправляемые (виртуальные жесткие диски и страничные BLOB-объекты) диски Azure с помощью Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: 17262978c9600b75a1ddf945cf170fea6ac4f8ce
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34756938"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Поиск и удаление неподключенных управляемых и неуправляемых дисков Azure
Когда вы удаляете виртуальную машину в Azure, подключенные к ней диски не удаляются по умолчанию. Это предотвращает потерю данных из-за случайного удаления виртуальной машины. Тем не менее вы продолжаете платить за неподключенные диски. В этой статье показано, как найти и удалить все неподключенные диски и сократить затраты. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Управляемые диски: поиск и удаление неподключенных дисков 

В следующем скрипте показано, как с помощью свойства **ManagedBy** найти неподключенные [управляемые диски](managed-disks-overview.md). В свойстве **ManagedBy** хранится идентификатор ресурса виртуальной машины, к которой подключен управляемый диск. Если управляемый диск не подключен, свойство **ManagedBy** имеет значение NULL. Сценарий проверяет все управляемые диски в подписке Azure. Когда скрипт находит управляемый диск со значением свойства **ManagedBy** равным null, он определяет, что диск не подключен.

>[!IMPORTANT]
>Сначала запустите скрипт, установив для переменной **deleteUnattachedDisks** значение 0. Это действие позволяет найти и просмотреть все неподключенные управляемые диски.
>
>После просмотра всех неподключенных дисков запустите скрипт еще раз и установите для переменной **deleteUnattachedDisks** значение 1. Это действие позволяет удалить все неподключенные управляемые диски.
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Неуправляемые диски: поиск и удаление неподключенных дисков 

Неуправляемые диски — это VHD-файлы, которые хранятся в [учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md) как [страничные BLOB-объекты](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs). В следующем скрипте показано, как с помощью свойства **LeaseStatus** найти неподключенные неуправляемые диски (страничные BLOB-объекты). Если неуправляемый диск подключен к виртуальной машине, свойство **LeaseStatus** имеет значение **Locked**. Если неуправляемый диск не подключен к виртуальной машине, свойство **LeaseStatus** имеет значение **Unlocked**. Сценарий проверяет все неуправляемые диски во всех учетных записях хранения Azure в подписке Azure. Когда скрипт находит неуправляемый диск со значением свойства **LeaseStatus** равным **Unlocked**, он определяет, что диск не подключен.

>[!IMPORTANT]
>Сначала запустите скрипт, установив для переменной **deleteUnattachedVHDs** значение 0. Это действие позволяет найти и просмотреть все неподключенные неуправляемые виртуальные жесткие диски.
>
>После просмотра всех неподключенных дисков запустите скрипт еще раз и установите для переменной **deleteUnattachedVHDs** значение 1. Это действие позволяет удалить все неподключенные неуправляемые виртуальные жесткие диски.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье об [удалении учетной записи хранения](../../storage/common/storage-create-storage-account.md) и в записи блога о [выявлении потерянных дисков с помощью PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/).



