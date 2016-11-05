---
title: Изменение размера виртуальной машины Linux | Microsoft Docs
description: В статье рассматривается, как увеличить и уменьшить масштаб виртуальной машины Linux, изменяя ее размер.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: ''
tags: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mikewasson

---
# Изменение размера виртуальной машины Linux
## Обзор
После того как вы подготовили виртуальную машину, ее можно увеличить или уменьшить, изменив [размер][vm-sizes]. В некоторых случаях сначала необходимо освободить виртуальную машину. Это может случиться, если новый размер недоступен в кластере оборудования, в котором она размещена.

В этой статье описывается изменение размера виртуальной машины Linux с помощью [интерфейса командной строки Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Классическая модель развертывания.

## Изменение размера виртуальной машины Linux
Чтобы изменить размер виртуальной машины, сделайте следующее:

1. Выполните приведенную ниже команду интерфейса командной строки. Эта команда выводит список размеров виртуальной машины, доступных в кластере оборудования, в котором она размещена.
   
    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```
2. Если в списке есть нужный размер, выполните следующую команду, чтобы изменить размер виртуальной машины.
   
    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```
   
    Во время этого процесса будет выполнена перезагрузка виртуальной машины. После перезагрузки существующие диски ОС и данных будут переназначены. Данные на временном диске будут утеряны.
   
    Применение параметра `--enable-boot-diagnostics` позволяет включить [диагностику загрузки][boot-diagnostics] для регистрации ошибок запуска.
3. Если нужный размер отсутствует в списке, выполните следующие команды, чтобы отозвать виртуальную машину, изменить ее размер и перезагрузить.
   
    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```
   
   > [!WARNING]
   > Освобождение виртуальной машины также освобождает все назначенные ей динамические IP-адреса. Это не влияет на диски ОС и данных.
   > 
   > 

## Дальнейшие действия
Для дополнительной масштабируемости запустите несколько экземпляров виртуальных машин и разверните их. Дополнительные сведения см. в статье [Автоматическое масштабирование машин Linux в наборе масштабирования виртуальных машин][scale-set].

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0824_2016-->