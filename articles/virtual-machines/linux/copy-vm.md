---
title: Копирование виртуальной машины Linux с помощью Azure CLI 2.0 | Документация Майкрософт
description: Узнайте, как создать копию виртуальной машины Linux в Azure с помощью Azure CLI 2.0 и Управляемых дисков.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 66f2789d717816f5be3fd8b298819825f8cd87f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Создание копии виртуальной машины Linux с помощью Azure CLI 2.0 и Управляемых дисков


В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux, используя Azure CLI 2.0 и модель развертывания с помощью Azure Resource Manager. Эти действия можно также выполнить с помощью [Azure CLI 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Кроме того, можно [передать VHD и создать виртуальную машину на его основе](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>предварительным требованиям


-   Установка [Azure CLI 2.0](/cli/azure/install-az-cli2)

-   Войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

-   Выберите виртуальную машину Azure, которая послужит источником копии.

## <a name="step-1-stop-the-source-vm"></a>Шаг 1. Остановка исходной виртуальной машины


Отмените выделение исходной виртуальной машины, выполнив команду [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
В следующем примере отменяется распределение виртуальной машины **myVM**, входящей в группу ресурсов **myResourceGroup**.

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Шаг 2. Копирование исходной виртуальной машины


Чтобы скопировать виртуальную машину, необходимо создать копию ее виртуального жесткого диска. Этот процесс позволяет создать специализированный VHD в качестве управляемого диска, содержащего такие же конфигурацию и параметры, как у исходной виртуальной машины.

Дополнительные сведения об управляемых дисках Azure см. в [этой статье](../windows/managed-disks-overview.md). 

1.  Получите список виртуальных машин и имен их дисков ОС, выполнив команду [az vm list](/cli/azure/vm#az_vm_list). В следующем примере создается список виртуальных машин, входящих в группу ресурсов **myResourceGroup**.
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Скопируйте диск, создав управляемый диск с помощью команды [az disk create](/cli/azure/disk#az_disk_create). В следующем примере создается диск **myCopiedDisk** на основе управляемого диска **myDisk**.

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Убедитесь, что этот управляемый диск теперь входит в нужную группу ресурсов, выполнив команду [az disk list](/cli/azure/disk#az_disk_list). В следующем примере выводится список управляемых дисков, входящих в группу ресурсов **myResourceGroup**.

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Шаг 3. Настройка виртуальной сети


Следующие необязательные шаги позволяют создать виртуальную сеть, подсеть, общедоступный IP-адрес и виртуальную сетевую карту.

Если копирование виртуальной машины выполняется для устранения неполадок или для создания дополнительных развертываний, вам может потребоваться создать виртуальную машину в новой виртуальной сети.

Если для копируемых виртуальных машин нужна отдельная инфраструктура виртуальный сети, выполните следующие шаги. Если вам не нужно создавать виртуальную сеть, перейдите к разделу [Шаг 4. Создание виртуальной машины](#step-4-create-a-vm).

1.  Создайте виртуальную сеть, выполнив команду [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создаются виртуальная сеть **myVnet** и подсеть **mySubnet**.

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Создайте общедоступный IP-адрес, выполнив команду [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). В следующем примере создается общедоступный IP-адрес **myPublicIP** с DNS-именем **mypublicdns**. (DNS-имя должно быть уникальным, поэтому укажите уникальное имя.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Создайте сетевую карту, выполнив команду [az network nic create](/cli/azure/network/nic#az_network_nic_create).
    В следующем примере создается сетевая карта **myNic**, подключенная к подсети **mySubnet**.

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Шаг 4. Создание виртуальной машины

Теперь можно создать виртуальную машину, выполнив команду [az vm create](/cli/azure/vm#az_vm_create).

Укажите скопированный управляемый диск в качестве диска ОС (--attach-os-disk) следующим образом.

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать, как управлять виртуальной машиной с помощью Azure CLI, прочитайте статью [Команды Azure CLI в режиме Resource Manager](../azure-cli-arm-commands.md).
