---
title: "Расширение диска операционной системы на виртуальной машине Linux в Azure | Документация Майкрософт"
description: "Узнайте, как расширить виртуальный диск операционной системы на виртуальной машине Linux с использованием Azure CLI и модели развертывания Resource Manager"
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
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2826f825b2d34005ce6e7142dd4371285a452ca8
ms.openlocfilehash: bd1952281dde6f262848d1520995efdb131a3b38


---

# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli"></a>Расширение дисков операционной системы на виртуальной машине Linux с помощью Azure CLI
Как правило, размер виртуального жесткого диска по умолчанию для операционной системы на виртуальной машине Linux в Azure составляет 30 ГБ. Вы можете [добавить диски данных](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), чтобы предоставить дополнительное место для хранения, а также расширить диск операционной системы. В этой статье подробно описано, как расширить диск ОС виртуальной машины Linux, использующей неуправляемые диски, с помощью Azure CLI.


## <a name="prerequisites"></a>Предварительные требования
Вам потребуется установить [последнюю версию Azure CLI](../xplat-cli-install.md) и войти в [учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/) в режиме Resource Manager, как показано ниже:

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup` и `myVM`.


## <a name="expand-os-disk"></a>Расширение диска операционной системы

1. Нельзя выполнять операции с виртуальными жесткими дисками на работающей виртуальной машине. В следующем примере останавливается работа виртуальной машины `myVM` в группе ресурсов `myResourceGroup` и отменяется ее распределение:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Операция `azure vm stop` не освобождает вычислительные ресурсы. Чтобы освободить вычислительные ресурсы, используйте `azure vm deallocate`. Для расширения виртуального жесткого диска следует отменить распределение виртуальной машины.

2. Обновите размер неуправляемого диска ОС, используя команду `azure vm set`. В следующем примере обновляется виртуальная машина `myVM` в группе ресурсов `myResourceGroup` и для нее устанавливается размер `50` ГБ:

    ```azurecli
    azure vm set --resource-group myResourceGroup --name myVM --new-os-disk-size 50
    ```

3. Запустите виртуальную машину следующим образом:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. Подключитесь к виртуальной машине по протоколу SSH, используя соответствующие учетные данные. Чтобы проверить, изменился ли размер диска операционной системы, используйте `df -h`. В следующем примере выходных данных показано, что теперь размер основного раздела (`/dev/sda1`) составляет 50 ГБ:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Дальнейшие действия
Если вам требуется дополнительное место для хранения, можно также [добавить диски данных в виртуальную машину Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Дополнительные сведения о шифровании диска см. в статье [Encrypt disks on a Linux VM using the Azure CLI](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Шифрование дисков на виртуальной машине Linux с помощью Azure CLI).



<!--HONumber=Feb17_HO2-->


