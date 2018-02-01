---
title: "Поиск и удаление неподключенных управляемых и неуправляемых дисков Azure | Документация Майкрософт"
description: "Сведения о том, как найти и удалить неподключенные управляемые и неуправляемые (виртуальные жесткие диски и страничные BLOB-объекты) диски Azure с помощью Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Поиск и удаление неподключенных управляемых и неуправляемых дисков Azure
Когда вы удаляете виртуальную машину в Azure, подключенные к ней диски не удаляются по умолчанию. Это предотвращает потерю данных из-за случайного удаления виртуальной машины, но при этом вы продолжаете платить за неподключенные диски. Ознакомьтесь с этой статьей, чтобы найти и удалить все неподключенные диски и сократить затраты. 


## <a name="find-and-delete-unattached-managed-disks"></a>Поиск и удаление неподключенных управляемых дисков Azure 

В следующем сценарии показано, как с помощью свойства *ManagedBy* найти неподключенные [управляемые диски](managed-disks-overview.md). Он проходит по всем управляемым дискам в подписке и проверяет, имеет ли свойство *ManagedBy* значение null, чтобы найти неподключенные управляемые диски. В свойстве *ManagedBy* хранится идентификатор ресурса виртуальной машины, к которой подключен управляемый диск.

Мы настоятельно рекомендуем сначала выполнить сценарий, задав для переменной *deleteUnattachedDisks* значение 0, чтобы просмотреть все неподключенные диски. После просмотра неподключенных дисков задайте для переменной *deleteUnattachedDisks* значение 1 и выполните сценарий, чтобы удалить все неподключенные диски.

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Поиск и удаление неподключенных неуправляемых дисков Azure 

Неуправляемые диски — это VHD-файлы, которые хранятся в виде страничных BLOB-объектов (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) в [учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md). В следующем сценарии показано, как с помощью свойства *LeaseStatus* найти неподключенные неуправляемые диски (страничные BLOB-объекты). Он проходит по всем неуправляемым дискам во всех учетных записях хранения в подписке и проверяет, имеет ли свойство *LeaseStatus* значение unlocked, чтобы найти неподключенные неуправляемые диски. Свойство *LeaseStatus* имеет значение locked, если неуправляемый диск подключен к виртуальной машине.

Мы настоятельно рекомендуем сначала выполнить сценарий, задав для переменной *deleteUnattachedVHDs* значение 0, чтобы просмотреть все неподключенные диски. После просмотра неподключенных дисков задайте для переменной *deleteUnattachedVHDs* значение 1 и выполните сценарий, чтобы удалить все неподключенные диски.


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

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

[Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md)




