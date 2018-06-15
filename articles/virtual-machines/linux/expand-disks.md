---
title: Расширение виртуальных жестких дисков виртуальной машины Linux в Azure | Документация Майкрософт
description: Узнайте, как расширить виртуальные жесткие диски на виртуальной машине Linux с помощью Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: c3dcd2f9c71b64ac48c2c7b6f51d2694d649c15e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30291288"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI
Как правило, размер виртуального жесткого диска по умолчанию для операционной системы на виртуальной машине Linux в Azure составляет 30 ГБ. Вы можете [добавить диски данных](add-disk.md), чтобы предоставить дополнительное место для хранения, а также расширить существующий диск данных. В этой статье подробно описано, как расширить управляемые диски для виртуальной машины Linux с помощью Azure CLI 2.0. С помощью [Azure CLI 1.0](expand-disks-nodejs.md) можно также расширить неуправляемые диски операционной системы.

> [!WARNING]
> Всегда проверяйте, созданы ли резервные копии данных, прежде чем выполнять операции по изменению размера диска. Дополнительные сведения см. в статье [Архивация виртуальных машин Linux в Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Развертывание управляемого диска Azure
Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

Для этой статьи потребуется существующая виртуальная машина в Azure с хотя бы одним подключенным и подготовленным диском данных. Если у вас нет виртуальной машины, которую можно использовать, см. раздел [о создании и подготовке виртуальной машины с дисками данных](tutorial-manage-disks.md#create-and-attach-disks).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup* и *myVM*.

1. Нельзя выполнять операции с виртуальными жесткими дисками на работающей виртуальной машине. Отмените подготовку виртуальной машины, выполнив команду [az vm deallocate](/cli/azure/vm#az_vm_deallocate). В следующем примере отменяется распределение виртуальной машины *myVM*, входящей в группу ресурсов с именем *myResourceGroup*.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Для расширения виртуального жесткого диска следует отменить распределение виртуальной машины. Операция `az vm stop` не освобождает вычислительные ресурсы. Чтобы освободить вычислительные ресурсы, используйте `az vm deallocate`.

2. Просмотрите список управляемых дисков в группе ресурсов, выполнив команду [az disk list](/cli/azure/disk#az_disk_list). В следующем примере выводится список управляемых дисков, входящих в группу ресурсов с именем *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Расширьте диск, выполнив команду [az disk update](/cli/azure/disk#az_disk_update). В следующем примере размер управляемого диска *myDataDisk* увеличивается до *200* ГБ:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > При расширении управляемого диска его новый размер сопоставляется с размером ближайшего управляемого диска. Таблица с размерами и ценовыми категориями доступных управляемых дисков доступна в разделе [Цены и выставление счетов](../windows/managed-disks-overview.md#pricing-and-billing).

3. Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#az_vm_start). В следующем примере запускается виртуальная машина *myVM*, входящая в группу ресурсов с именем *myResourceGroup*.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Развертывание раздела диска и файловой системы
Чтобы использовать развернутый диск, необходимо развернуть основной раздел и файловую систему.

1. Подключитесь к виртуальной машине по протоколу SSH, используя соответствующие учетные данные. Вы можете получить общедоступный IP-адрес виртуальной машины с помощью команды [az vm show](/cli/azure/vm#az_vm_show).

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. Чтобы использовать развернутый диск, необходимо развернуть основной раздел и файловую систему.

    a. Если диск подключен, отключите его.

    ```bash
    sudo umount /dev/sdc1
    ```

    Б. Чтобы просмотреть сведения о диске и изменить размер раздела, используйте команду `parted`.

    ```bash
    sudo parted /dev/sdc
    ```

    Просмотрите сведения о структуре существующего раздела с помощью команды `print`. В результате будут выведены выходные данные, как в примере ниже, указывающие, что размер основного диска составляет 215 ГБ.

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Разверните раздел с помощью команды `resizepart`. Введите номер (*1*) и размер для нового раздела.

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Чтобы закрыть командную строку, введите `quit`.

3. Изменив размер раздела, проверьте его целостность с помощью команды `e2fsck`.

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. Теперь измените размер файловой системы, выполнив команду `resize2fs`.

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. Подключите раздел к необходимому расположению, например `/datadrive`.

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. Чтобы проверить, изменился ли размер диска операционной системы, используйте `df -h`. В следующем примере выходных данных показано, что теперь размер основного раздела (*/dev/sdc1*) составляет 200 ГБ:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Дополнительная информация
Если вам требуется дополнительное место для хранения, можно также [добавить диски данных в виртуальную машину Linux](add-disk.md). Дополнительные сведения о шифровании диска см. в статье [Encrypt disks on a Linux VM using the Azure CLI](encrypt-disks.md) (Шифрование дисков на виртуальной машине Linux с помощью Azure CLI).
