---
title: Как изменить размер виртуальной машины Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: В статье рассматривается, как увеличить и уменьшить масштаб виртуальной машины Linux, изменяя ее размер.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4407a3662e6a50d650ab4ecc6dd1c2dad0c53bdb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Изменение размера виртуальной машины Linux с помощью Azure CLI 1.0

## <a name="overview"></a>Обзор

После того как вы подготовили виртуальную машину, ее можно увеличить или уменьшить, изменив [размер][vm-sizes]. В некоторых случаях сначала необходимо освободить виртуальную машину. Это может случиться, если новый размер недоступен в кластере оборудования, в котором она размещена.

В этой статье описывается изменение размера виртуальной машины Linux с помощью [интерфейса командной строки Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#resize-a-linux-vm) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="resize-a-linux-vm"></a>Изменение размера виртуальной машины Linux
Чтобы изменить размер виртуальной машины, сделайте следующее:

1. Выполните приведенную ниже команду интерфейса командной строки. Эта команда выводит список размеров виртуальной машины, доступных в кластере оборудования, в котором она размещена.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Если в списке есть нужный размер, выполните следующую команду, чтобы изменить размер виртуальной машины.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    Во время этого процесса будет выполнена перезагрузка виртуальной машины. После перезагрузки существующие диски ОС и данных будут переназначены. Данные на временном диске будут утеряны.
   
    Применение параметра `--enable-boot-diagnostics` позволяет включить [диагностику загрузки][boot-diagnostics] для регистрации ошибок запуска.
3. Если нужный размер отсутствует в списке, выполните следующие команды, чтобы отозвать виртуальную машину, изменить ее размер и перезагрузить.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Освобождение виртуальной машины также освобождает все назначенные ей динамические IP-адреса. Это не влияет на диски ОС и данных.
   > 
   > 

## <a name="next-steps"></a>Дополнительная информация
Для дополнительной масштабируемости запустите несколько экземпляров виртуальных машин и разверните их. Дополнительные сведения см. в статье [Автоматическое масштабирование машин Linux в наборе масштабирования виртуальных машин][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
