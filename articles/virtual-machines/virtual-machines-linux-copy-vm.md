---
title: "Копирование виртуальной машины Linux с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как создать копию виртуальной машины Linux в Azure, развернутой в рамках модели Resource Manager, с помощью Azure CLI 2.0 (предварительная версия)."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Создание копии виртуальной машины Linux с помощью Azure CLI 2.0 (предварительная версия)
В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux, используя модель развертывания с помощью Azure Resource Manager.

Скопируйте операционную систему и диски данных в новый контейнер, а затем настройте сетевые ресурсы и создайте виртуальную машину.

Кроме того, можно [передать пользовательский образ диска и создать виртуальную машину на его основе](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions"></a>Версии интерфейса командной строки
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки:

* [Azure CLI 1.0.](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Для классической модели развертывания и модели развертывания с помощью Resource Manager.
* Azure CLI 2.0 (предварительная версия). Интерфейс командной строки нового поколения для модели развертывания с помощью Resource Manager, описанный в этой статье.

## <a name="prerequisites"></a>Предварительные требования
* Установите последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войдите в учетную запись Azure, выполнив команду [az login](/cli/azure/#login).
* При создании копии используйте в качестве источника виртуальную машину Azure.

## <a name="step-1-stop-the-source-vm"></a>Шаг 1. Остановка исходной виртуальной машины
Отмените выделение исходной виртуальной машины, выполнив команду [az vm deallocate](/cli/azure/vm#deallocate). В следующем примере освобождается виртуальная машина `myVM`, входящая в группу ресурсов `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Шаг 2. Копирование исходной виртуальной машины
Чтобы скопировать виртуальную машину, необходимо создать копию ее виртуального жесткого диска. Этот процесс позволяет создать специализированную виртуальную машину, конфигурация и параметры которой будут такими же, как у исходной виртуальной машины.

Процессы копирования виртуального диска различаются для Управляемых дисков Azure и неуправляемых дисков. Управляемые диски полностью контролируются платформой Azure и не требуют никакой подготовки или выделения места. Так как управляемые диски являются ресурсами верхнего уровня, с ними гораздо проще работать. Достаточно напрямую скопировать этот дисковый ресурс.

Дополнительные сведения об Управляемых дисках Azure см. в [обзоре Управляемых дисков Azure](../storage/storage-managed-disks-overview.md).

В зависимости от типа хранилища, используемого для исходной виртуальной машины, следуйте инструкциям, указанным в одном из следующих двух разделов, а затем перейдите к разделу "Шаг 3. Настройка виртуальной сети".

### <a name="managed-disks"></a>Управляемые диски

1. Получите список виртуальных машин и имен соответствующих управляемых дисков с операционными системами, выполнив команду [az vm list](/cli/azure/vm#list). В следующем примере создается список виртуальных машин, входящих в группу ресурсов `myResourceGroup`:

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. Чтобы скопировать диск, создайте управляемый диск, выполнив команду [az disk create](/cli/azure/disk#create). В следующем примере создается диск `myCopiedDisk` на основе управляемого диска `myDisk`:

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. Убедитесь, что этот управляемый диск теперь входит в нужную группу ресурсов, выполнив команду [az disk list](/cli/azure/disk#list). В следующем примере создается список управляемых дисков, входящих в группу ресурсов `myResourceGroup`:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. Перейдите к разделу [Шаг 3. Настройка виртуальной сети](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Неуправляемые диски

1. Чтобы создать копию виртуального жесткого диска, вам потребуются ключи учетной записи хранения Azure и универсальный код ресурса (URI) диска. Ключи учетной записи хранения можно просмотреть, выполнив команду [az storage account keys list](/cli/azure/storage/account/keys#list).

 В следующем примере отображается список ключей для учетной записи хранения `mystorageaccount` в группе ресурсов `myResourceGroup`:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. Выполните команду [az vm list](/cli/azure/vm#list), чтобы просмотреть список виртуальных машин и их универсальные коды ресурсов. В следующем примере отображается список виртуальных машин, входящих в группу ресурсов `myResourceGroup`:

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. Скопируйте виртуальный жесткий диск, выполнив команду [az storage blob copy start](/cli/azure/storage/blob/copy#start). Чтобы указать необходимые ключи учетной записи хранения и универсальный код ресурса виртуального жесткого диска, используйте сведения из списков, полученных с помощью команд `az storage account keys` и `az vm`.

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>Шаг 3. Настройка виртуальной сети
Следующие необязательные шаги позволяют создать виртуальную сеть, подсеть, общедоступный IP-адрес и виртуальную сетевую карту.

Если копирование виртуальной машины выполняется для устранения неполадок или для создания дополнительных развертываний, вам может потребоваться создать виртуальную машину в новой виртуальной сети.

Если для копируемых виртуальных машин нужна отдельная инфраструктура виртуальный сети, выполните следующие шаги. Если вам не нужно создавать виртуальную сеть, перейдите к разделу [Шаг 4. Создание виртуальной машины](#create-a-vm).

1. Создайте виртуальную сеть, выполнив команду [az network vnet create](/cli/azure/network/vnet#create). В следующем примере создается виртуальная сеть `myVnet` и подсеть `mySubnet`:

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. Создайте общедоступный IP-адрес, выполнив команду [az network public-ip create](/cli/azure/network/public-ip#create). В следующем примере создается общедоступный IP-адрес `myPublicIP` с DNS-именем `mypublicdns`. (DNS-имя должно быть уникальным, поэтому укажите уникальное имя.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. Создайте сетевую карту, выполнив команду [az network nic create](/cli/azure/network/nic#create). В следующем примере создается сетевая карта `myNic`, подключенная к подсети `mySubnet`:

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>Шаг 4. Создание виртуальной машины
Теперь можно создать виртуальную машину, выполнив команду [az vm create](/cli/azure/vm#create). Как и при копировании диска, этот процесс немного отличается для управляемых и неуправляемых дисков. В зависимости от типа хранилища, используемого для исходной виртуальной машины, следуйте инструкциям, указанным в одном из следующих двух разделов.

### <a name="managed-disks"></a>Управляемые диски
1. Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm#create).
2. Укажите скопированный управляемый диск в качестве диска операционной системы (`--attach-os-disk`) следующим образом:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>Неуправляемые диски
1. Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create).
2. Укажите учетную запись хранения, имя контейнера и виртуальный жесткий диск, использованные при копировании диска путем выполнения команды `az storage blob copy start` (`--image`), следующим образом:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>Дальнейшие действия
Сведения об управлении виртуальной машиной с помощью Azure CLI см. в статье [Команды Azure CLI в режиме Resource Manager](azure-cli-arm-commands.md).

