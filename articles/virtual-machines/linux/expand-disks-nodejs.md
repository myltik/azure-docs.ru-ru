---
title: Расширение диска операционной системы на виртуальной машине Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Узнайте, как расширить виртуальный диск операционной системы на виртуальной машине Linux с использованием Azure CLI 1.0 и модели развертывания Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: f81054727bb1f0e8ffa752783e866a72d573589d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Расширение дисков операционной системы на виртуальной машине Linux с помощью Azure CLI 1.0
Как правило, размер виртуального жесткого диска по умолчанию для операционной системы на виртуальной машине Linux в Azure составляет 30 ГБ. Вы можете [добавить диски данных](add-disk.md), чтобы предоставить дополнительное место для хранения, а также расширить диск операционной системы. В этой статье подробно описано, как расширить диск ОС виртуальной машины Linux, использующей неуправляемые диски, с помощью Azure CLI 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#prerequisites) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](expand-disks.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="prerequisites"></a>предварительным требованиям
Вам потребуется установить [последнюю версию Azure CLI 1.0](../../cli-install-nodejs.md) и войти в [учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/) в режиме Resource Manager, как показано ниже:

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup* и *myVM*.

## <a name="expand-os-disk"></a>Расширение диска операционной системы

1. Нельзя выполнять операции с виртуальными жесткими дисками на работающей виртуальной машине. В следующем примере завершается работа и отменяется распределение виртуальной машины *myVM*, входящей в группу ресурсов с именем *myResourceGroup*.

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Операция `azure vm stop` не освобождает вычислительные ресурсы. Чтобы освободить вычислительные ресурсы, используйте `azure vm deallocate`. Для расширения виртуального жесткого диска следует отменить распределение виртуальной машины.

2. Обновите размер неуправляемого диска ОС, используя команду `azure vm set`. В следующем примере выполняется обновление виртуальной машины *myVM*, входящей в группу ресурсов с именем *myResourceGroup*, до *50* ГБ.

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Запустите виртуальную машину следующим образом:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. Подключитесь к виртуальной машине по протоколу SSH, используя соответствующие учетные данные. Чтобы проверить, изменился ли размер диска операционной системы, используйте `df -h`. В следующем примере выходных данных показано, что теперь размер основного раздела (*/dev/sda1*) составляет 50 ГБ:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Дополнительная информация
Если вам требуется дополнительное место для хранения, можно также [добавить диски данных в виртуальную машину Linux](add-disk.md). Дополнительные сведения о шифровании диска см. в статье [Encrypt disks on a Linux VM using the Azure CLI](encrypt-disks.md) (Шифрование дисков на виртуальной машине Linux с помощью Azure CLI).
