---
title: "Поиск и удаление неподключенных управляемых и неуправляемых дисков Azure | Документация Майкрософт"
description: "Сведения о том, как найти и удалить неподключенные управляемые и неуправляемые диски Azure (виртуальные жесткие диски и страничные BLOB-объекты) с помощью Azure CLI."
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Поиск и удаление неподключенных управляемых и неуправляемых дисков Azure
Когда вы удаляете виртуальную машину в Azure, подключенные к ней диски не удаляются по умолчанию. Это предотвращает потерю данных из-за случайного удаления виртуальной машины, но при этом вы продолжаете платить за неподключенные диски без необходимости. Ознакомьтесь с этой статьей, чтобы найти и удалить все неподключенные диски и сократить затраты. 


## <a name="find-and-delete-unattached-managed-disks"></a>Поиск и удаление неподключенных управляемых дисков Azure 

В следующем скрипте показано, как с помощью свойства ManagedBy найти неподключенные управляемые диски.  Он проходит по всем управляемым дискам в подписке и проверяет, имеет ли свойство *ManagedBy* значение NULL, чтобы найти неподключенные управляемые диски. В свойстве *ManagedBy* хранится идентификатор ресурса виртуальной машины, к которой подключен управляемый диск. 

Мы настоятельно рекомендуем сначала выполнить скрипт, задав для переменной *deleteUnattachedDisks* значение 0, чтобы просмотреть все неподключенные диски. После просмотра неподключенных дисков задайте для переменной *deleteUnattachedDisks* значение 1 и выполните скрипт, чтобы удалить все неподключенные диски.

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Поиск и удаление неподключенных неуправляемых дисков Azure 

Неуправляемые диски — это VHD-файлы, которые хранятся в [учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md) как [страничные BLOB-объекты](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs). В следующем скрипте показано, как с помощью свойства LeaseStatus найти неподключенные неуправляемые диски (страничные BLOB-объекты). Он проходит по всем неуправляемым дискам во всех учетных записях хранения в подписке и проверяет, имеет ли свойство *LeaseStatus* значение unlocked, чтобы найти неподключенные неуправляемые диски. Свойство *LeaseStatus* имеет значение locked, если неуправляемый диск подключен к виртуальной машине. 

Мы настоятельно рекомендуем сначала выполнить скрипт, задав для переменной *deleteUnattachedVHDs* значение 0, чтобы просмотреть все неподключенные диски. После просмотра неподключенных дисков задайте для переменной *deleteUnattachedVHDs* значение 1 и выполните скрипт, чтобы удалить все неподключенные диски.


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>Дополнительная информация

[Удаление учетной записи хранения](../../storage/common/storage-create-storage-account.md)



