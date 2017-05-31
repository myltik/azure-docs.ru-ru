---
title: "Расширение виртуальных жестких дисков виртуальной машины Linux в Azure | Документация Майкрософт"
description: "Узнайте, как расширить виртуальные жесткие диски на виртуальной машине Linux с помощью Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4a0b8254ec80576576afde7af34828025d1d2f0a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI
Как правило, размер виртуального жесткого диска по умолчанию для операционной системы на виртуальной машине Linux в Azure составляет 30 ГБ. Вы можете [добавить диски данных](add-disk.md), чтобы предоставить дополнительное место для хранения, а также расширить диск операционной системы или имеющийся диск данных. В этой статье подробно описано, как расширить управляемые диски для виртуальной машины Linux с помощью Azure CLI 2.0. С помощью [Azure CLI 1.0](expand-disks-nodejs.md) можно также расширить неуправляемые диски операционной системы.

## <a name="expand-disk"></a>Расширение диска
Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup* и *myVM*.

1. Нельзя выполнять операции с виртуальными жесткими дисками на работающей виртуальной машине. Отмените подготовку виртуальной машины, выполнив команду [az vm deallocate](/cli/azure/vm#deallocate). В следующем примере отменяется распределение виртуальной машины *myVM*, входящей в группу ресурсов с именем *myResourceGroup*.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Операция `az vm stop` не освобождает вычислительные ресурсы. Чтобы освободить вычислительные ресурсы, используйте `az vm deallocate`. Для расширения виртуального жесткого диска следует отменить распределение виртуальной машины.

2. Просмотрите список управляемых дисков в группе ресурсов, выполнив команду [az disk list](/cli/azure/disk#list). В следующем примере выводится список управляемых дисков, входящих в группу ресурсов с именем *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Расширьте диск, выполнив команду [az disk update](/cli/azure/disk#update). В следующем примере размер управляемого диска *myDataDisk* увеличивается до *200* ГБ:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > При расширении управляемого диска его новый размер сопоставляется с размером ближайшего управляемого диска. Таблица с размерами и ценовыми категориями доступных управляемых дисков доступна в разделе [Цены и выставление счетов](../../storage/storage-managed-disks-overview.md#pricing-and-billing).

3. Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#start). В следующем примере запускается виртуальная машина *myVM*, входящая в группу ресурсов с именем *myResourceGroup*.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. Подключитесь к виртуальной машине по протоколу SSH, используя соответствующие учетные данные. Чтобы проверить, изменился ли размер диска операционной системы, используйте `df -h`. В следующем примере выходных данных показано, что теперь размер основного раздела (*/dev/sdc1*) составляет 200 ГБ:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>Дальнейшие действия
Если вам требуется дополнительное место для хранения, можно также [добавить диски данных в виртуальную машину Linux](add-disk.md). Дополнительные сведения о шифровании диска см. в статье [Encrypt disks on a Linux VM using the Azure CLI](encrypt-disks.md) (Шифрование дисков на виртуальной машине Linux с помощью Azure CLI).

