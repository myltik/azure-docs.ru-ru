---
title: "Установка драйвера серии N для Windows | Документация Майкрософт"
description: "Как установить драйверы NVIDIA GPU для виртуальных машин серии N под управлением Windows в Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: feb9370f0241fd860749d32f5db5842cb18463f1


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>Установка драйверов GPU для виртуальных машин серии N
Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Windows Server, необходимо установить графические драйверы NVIDIA на каждую виртуальную машину после развертывания. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




## <a name="supported-gpu-drivers"></a>Поддерживаемые драйверы GPU

Подключитесь к каждой виртуальной машине серии N с помощью удаленного рабочего стола. Скачайте, извлеките и установите поддерживаемый драйвер для своей операционной системы Windows. 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>Драйверы NVIDIA GRID для виртуальных машин NV

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (ZIP-файл)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (ZIP-файл)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Драйверы NVIDIA Tesla для виртуальных машин NC

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (ZIP-файл)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (ZIP-файл)



## <a name="verify-driver-installation"></a>Проверка установки драйверов

После установки драйвера на виртуальных машинах Azure серии NV их требуется перезагрузить. На виртуальных машинах NC перезагрузка не требуется.

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная конфигурация карты K80 на виртуальной машине Azure серии NC.

![Свойства драйвера GPU](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

Чтобы запросить состояние устройства GPU, выполните служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

![Состояние устройства NVIDIA](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).

* Разработчики приложений с ускорением GPU, предназначенные для графических процессоров NVIDIA Tesla, могут также скачать и установить [CUDA Toolkit 8](https://developer.nvidia.com/cuda-downloads).





<!--HONumber=Dec16_HO2-->


