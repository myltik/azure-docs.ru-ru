---
title: "Копирование виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия) | Документация Майкрософт"
description: "Узнайте, как создать копию виртуальной машины Linux в Azure, развернутой в рамках модели Resource Manager, с помощью интерфейса командной строки Azure 2.0 (предварительная версия)"
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
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>Создание копии виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия)
В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux, используя модель развертывания с помощью Resource Manager. Сначала следует скопировать операционную систему и диски данных в новый контейнер, а затем настроить сетевые ресурсы и создать виртуальную машину.

Кроме того, можно [передать пользовательский образ диска и создать виртуальную машину на его основе](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- Azure CLI 2.0 (предварительная версия) — интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).

## <a name="prerequisites"></a>Предварительные требования
- Вам нужно установить последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).
- При создании копии необходимо использовать в качестве источника виртуальную машину Azure.

## <a name="stop-the-vm"></a>Остановка виртуальной машины
Отмените подготовку исходной виртуальной машины командой [az vm deallocate](/cli/azure/vm#deallocate). В следующем примере отменяется подготовка виртуальной машины с именем `myVM`, входящей в группу ресурсов с именем `myResourceGroup`.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>Копирование виртуальной машины
Чтобы скопировать виртуальную машину, необходимо создать копию ее виртуального жесткого диска. Этот процесс позволяет создать специализированную виртуальную машину, конфигурация и параметры которой будут такими же, как у исходной виртуальной машины. Процессы копирования виртуального диска различаются для управляемых дисков Azure и неуправляемых дисков. Управляемые диски полностью контролируются платформой Azure и не требуют никакой подготовки или выделения места. Так как управляемые диски являются ресурсами верхнего уровня, с ними гораздо проще работать. Достаточно напрямую скопировать этот дисковый ресурс. Дополнительные сведения об управляемых дисках Azure см. в [этой статье](../storage/storage-managed-disks-overview.md). Выберите один из следующих шагов, соответствующий типу хранилища, используемого для исходной виртуальной машины.

- [Управляемые диски](#managed-disks)
- [Неуправляемые диски](#unmanaged-disks) 

### <a name="managed-disks"></a>Управляемые диски
Получите список виртуальных машин и имен соответствующих управляемых дисков с операционными системами, используя команду [az vm list](/cli/azure/vm#list). В следующем примере создается список виртуальных машин, входящих в группу ресурсов `myResourceGroup`.

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

Скопируйте диск, создав новый управляемый диск с помощью команды [az disk create](/cli/azure/disk#create). В следующем примере создается диск с именем `myCopiedDisk` на основе управляемого диска с именем `myDisk`.

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

С помощью команды [az disk list](/cli/azure/disk#list) убедитесь, что этот управляемый диск теперь входит в нужную группу ресурсов. В следующем примере создается список управляемых дисков, входящих в группу ресурсов `myResourceGroup`.

```azurecli
az disk list --resource-group myResourceGroup --output table
```

Переходите к [созданию и просмотру параметров виртуальной сети](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Неуправляемые диски
Чтобы создать копию виртуального жесткого диска, вам потребуются ключи учетной записи хранения и URI диска. Ключи учетной записи хранения можно получить командой [az storage account keys list](/cli/azure/storage/account/keys#list). В следующем примере отображается список ключей для учетной записи хранения с именем `mystorageaccount` в группе ресурсов с именем `myResourceGroup`.

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

Используйте [az vm list](/cli/azure/vm#list), чтобы просмотреть список виртуальных машин и их URI. В следующем примере отображается список виртуальных машин, входящих в группу ресурсов `myResourceGroup`.

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

Скопируйте VHD с помощью команды [az storage blob copy start](/cli/azure/storage/blob/copy#start). Используйте сведения, полученные с помощью команд **az storage account keys list** и **az vm list**, чтобы предоставить необходимые ключи учетной записи хранения и URI виртуального жесткого диска.

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>Настройка виртуальной сети
Следующие шаги позволяют создать новую виртуальную сеть, подсеть, общедоступный IP-адрес и виртуальный сетевой интерфейс (NIC). Эти действия являются необязательными. Если копирование виртуальной машины выполняется для устранения неполадок или для создания дополнительных развертываний, вам может потребоваться создать виртуальную машину в новой виртуальной сети. Если для копируемых виртуальных машин вам нужна отдельная инфраструктура виртуальный сети, выполните следующие шаги. В противном случае переходите сразу к разделу [Создание виртуальной машины](#create-a-vm).

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#create). В следующем примере создается виртуальная сеть `myVnet` и подсеть `mySubnet`.

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip#create). В следующем примере создается общедоступный IP-адрес `myPublicIP` с DNS-именем `mypublicdns`. DNS-имя должно быть уникальным, поэтому укажите собственное уникальное имя.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Создайте сетевой адаптер (NIC) с помощью команды [az network nic create](/cli/azure/network/nic#create). В следующем примере создается сетевой адаптер с именем `myNic`, подключенный к подсети `mySubnet`.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>Создание виртуальной машины
Теперь все готово, чтобы создать виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). Как и при копировании диска, процессы немного различаются для управляемых дисков Azure и неуправляемых дисков. Создайте виртуальную машину, выбрав подходящий вариант из следующих команд.

### <a name="managed-disks"></a>Управляемые диски
Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). Укажите скопированный управляемый диск в качестве диска операционной системы (`--attach-os-disk`) следующим образом:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>Неуправляемые диски
Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). Укажите учетную запись хранения, имя контейнера и виртуальный жесткий диск, который вы использовали при копировании с помощью команды **az storage blob copy start** (`--image`), следующим образом:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать, как использовать интерфейс командной строки Azure для управления новой виртуальной машиной, ознакомьтесь со статьей [Команды Azure CLI в режиме Resource Manager](azure-cli-arm-commands.md).




<!--HONumber=Feb17_HO2-->


