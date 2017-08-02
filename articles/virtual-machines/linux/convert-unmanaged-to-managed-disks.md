---
title: "Управляемые диски Azure. Преобразование виртуальной машины Linux с неуправляемыми дисками в Azure для использования управляемых дисков | Документация Майкрософт"
description: "Переключение виртуальной машины Linux, развернутой в рамках модели Resource Manager, с неуправляемых дисков на управляемые диски с помощью Azure CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 118d1efaaa0a962c7d13fae6ad5f447a60c4e85b
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---

# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Переключение виртуальной машины Linux с неуправляемых дисков на управляемые диски

При наличии виртуальных машин Linux, использующих неуправляемые диски, их можно преобразовать для использования управляемых дисков с помощью службы [Управляемые диски Azure](../../storage/storage-managed-disks-overview.md). При этом преобразуются диск операционной системы и все подключенные диски данных.

В этой статье показано, как преобразовать виртуальные машины с помощью Azure CLI. Если вам установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Преобразование одноэкземплярных виртуальных машин
В этом разделе описывается, как преобразовать одноэкземплярные виртуальные машины Azure с неуправляемыми дисками, чтобы они могли использовать Управляемые диски. (Если виртуальные машины находятся в группе доступности, ознакомьтесь со следующим разделом.) Этот процесс позволяет переключить виртуальные машины с неуправляемых дисков уровня "Премиум" (SSD) на управляемые диски уровня "Премиум" или с неуправляемых дисков уровня "Стандартный" (жесткие диски) на управляемые диски уровня "Стандартный".

1. Отмените выделение виртуальной машины с помощью команды [az vm deallocate](/cli/azure/vm#deallocate). В следующем примере освобождается виртуальная машина `myVM`, входящая в группу ресурсов `myResourceGroup`.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Преобразуйте виртуальную машину для использования управляемых дисков, выполнив команду [az vm convert](/cli/azure/vm#convert). Приведенный ниже процесс преобразовывает виртуальную машину `myVM`, включая ее диск ОС и все диски данных.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. После преобразования запустите виртуальную машину командой [az vm start](/cli/azure/vm#start). В следующем примере запускается виртуальная машина `myVM` в группе ресурсов `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Преобразование виртуальных машин в группе доступности

Если виртуальные машины, которые вы хотите преобразовать для использования управляемых дисков, входят в группу доступности, то необходимо сначала преобразовать эту группу доступности в управляемую группу доступности.

Перед преобразованием группы доступности нужно освободить все виртуальные машины в этой группе. Запланируйте преобразование всех виртуальных машин для использования управляемых дисков после того, как содержащая их группа доступности будет преобразована в управляемую группу доступности. Затем можно будет запустить все виртуальные машины и продолжить работу в обычном режиме.

1. Выведите список всех виртуальных машин в группе доступности, выполнив команду [az vm availability-set list](/cli/azure/vm/availability-set#list). В следующем примере выводится список виртуальных машин в группе доступности `myAvailabilitySet` в группе ресурсов `myResourceGroup`.

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Отмените выделение всех виртуальных машин командой [az vm deallocate](/cli/azure/vm#deallocate). В следующем примере освобождается виртуальная машина `myVM`, входящая в группу ресурсов `myResourceGroup`.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Преобразуйте группу доступности с помощью команды [az vm availability-set convert](/cli/azure/vm/availability-set#convert). В следующем примере преобразовывается группа доступности `myAvailabilitySet` в группе ресурсов `myResourceGroup`.

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Преобразуйте все виртуальные машины для использования управляемых дисков с помощью команды [az vm convert](/cli/azure/vm#convert). Приведенный ниже процесс преобразовывает виртуальную машину `myVM`, включая ее диск ОС и все диски данных.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. После преобразования запустите все виртуальные машины с помощью команды [az vm start](/cli/azure/vm#start). В следующем примере запускается виртуальная машина `myVM` в группе ресурсов `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="managed-disks-and-azure-storage-service-encryption"></a>Управляемые диски и шифрование службы хранилища Azure
Невозможно преобразовать неуправляемый диск в управляемый, выполнив предыдущие шаги, если неуправляемый диск размещен в учетной записи хранения, которая зашифрована или когда-либо шифровалась с помощью [шифрования службы хранилища Azure](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ниже приведены инструкции по копированию и использованию неуправляемых дисков, которые находятся в зашифрованной учетной записи хранения.

1. Выполните команду [az storage blob copy start](/cli/azure/storage/blob/copy#start), чтобы скопировать VHD в учетную запись хранения, для которой никогда не использовалось шифрование службы хранилища Azure.

2. Используйте скопированную виртуальную машину одним из следующих способов:

   * Создайте виртуальную машину, которая использует управляемые диски, и в процессе создания укажите этот VHD-файл с помощью команды [az vm create](/cli/azure/vm#create).

   * Можно также с помощью команды [az vm disk attach](/cli/azure/vm/disk#attach) подключить этот VHD к работающей виртуальной машине, использующей управляемые диски.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о возможностях хранения данных доступны в [обзоре Управляемых дисков Azure](../../storage/storage-managed-disks-overview.md).

