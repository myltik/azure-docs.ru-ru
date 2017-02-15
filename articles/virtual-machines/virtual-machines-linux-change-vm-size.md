---
title: "Изменение размера виртуальной машины Linux | Документация Майкрософт"
description: "В статье рассматривается, как увеличить и уменьшить масштаб виртуальной машины Linux, изменяя ее размер."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 788efb5d1cbbd5fd20096c54ca702b99eb2b5a18


---
# <a name="how-to-resize-a-linux-vm"></a>Изменение размера виртуальной машины Linux
## <a name="overview"></a>Обзор
После того как вы подготовили виртуальную машину, ее можно увеличить или уменьшить, изменив [размер][vm-sizes]. В некоторых случаях сначала необходимо освободить виртуальную машину. Это может случиться, если новый размер недоступен в кластере оборудования, в котором она размещена.

В этой статье описывается изменение размера виртуальной машины Linux с помощью [интерфейса командной строки Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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

## <a name="next-steps"></a>Дальнейшие действия
Для дополнительной масштабируемости запустите несколько экземпляров виртуальных машин и разверните их. Дополнительные сведения см. в статье [Автоматическое масштабирование машин Linux в наборе масштабирования виртуальных машин][scale-set]. 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Dec16_HO2-->


