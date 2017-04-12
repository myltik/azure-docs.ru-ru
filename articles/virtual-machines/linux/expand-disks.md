---
title: "Расширение виртуальных жестких дисков виртуальной машины Linux в Azure | Документация Майкрософт"
description: "Узнайте, как расширить виртуальные жесткие диски на виртуальной машине Linux с помощью Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/22/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 07d4afc5f3eedbdcd4686a64acccf40278f139c4
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli-20"></a>Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI 2.0
Как правило, размер виртуального жесткого диска по умолчанию для операционной системы на виртуальной машине Linux в Azure составляет 30 ГБ. Вы можете [добавить диски данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), чтобы предоставить дополнительное место для хранения, а также расширить диск операционной системы или имеющийся диск данных. В этой статье подробно описано, как расширить управляемые диски для виртуальной машины Linux с помощью Azure CLI 2.0. С помощью [Azure CLI 1.0](expand-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) можно также расширить неуправляемые диски операционной системы.

## <a name="expand-managed-disk"></a>Расширение управляемого диска
Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup` и `myVM`.

1. Нельзя выполнять операции с виртуальными жесткими дисками на работающей виртуальной машине. Отмените подготовку виртуальной машины, выполнив команду [az vm deallocate](/cli/azure/vm#deallocate). В следующем примере отменяется подготовка виртуальной машины с именем `myVM`, входящей в группу ресурсов с именем `myResourceGroup`.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Операция `az vm stop` не освобождает вычислительные ресурсы. Чтобы освободить вычислительные ресурсы, используйте `az vm deallocate`. Для расширения виртуального жесткого диска следует отменить распределение виртуальной машины.

2. Просмотрите список управляемых дисков в группе ресурсов, выполнив команду [az disk list](/cli/azure/disk#list). В следующем примере выводится список управляемых дисков, входящих в группу ресурсов `myResourceGroup`:

    ```azurecli
    az disk list -g myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Расширьте диск, выполнив команду [az disk update](/cli/azure/disk#update). В следующем примере размер управляемого диска `myDataDisk` увеличивается до `200` ГБ:

    ```azurecli
    az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 200
    ```

    > [!NOTE]
    > При расширении управляемого диска его новый размер сопоставляется с размером ближайшего управляемого диска. Таблица с размерами и ценовыми категориями доступных управляемых дисков доступна в разделе [Цены и выставление счетов](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#pricing-and-billing).

3. Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#start). В следующем примере запускается виртуальная машина `myVM` в группе ресурсов `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. Подключитесь к виртуальной машине по протоколу SSH, используя соответствующие учетные данные. Чтобы проверить, изменился ли размер диска операционной системы, используйте `df -h`. В следующем примере выходных данных показано, что теперь размер основного раздела (`/dev/sda1`) составляет 200 ГБ:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>Дальнейшие действия
Если вам требуется дополнительное место для хранения, можно также [добавить диски данных в виртуальную машину Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Дополнительные сведения о шифровании диска см. в статье [Encrypt disks on a Linux VM using the Azure CLI](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Шифрование дисков на виртуальной машине Linux с помощью Azure CLI).

