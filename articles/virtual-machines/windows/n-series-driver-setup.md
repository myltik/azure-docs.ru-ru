---
title: Установка драйвера серии N для Windows | Документация Майкрософт
description: Как установить драйверы NVIDIA GPU для виртуальных машин серии N под управлением Windows в Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61bcbe23ec3afd7091084dd0102010a9b8ef0b7c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Установка драйверов GPU для виртуальных машин серии N под управлением Windows Server
Чтобы воспользоваться преимуществами GPU виртуальных машин Azure серии N под управлением Windows Server 2016 или Windows Server 2012 R2, необходимо установить графические драйверы NVIDIA. В этой статье приводятся действия по установке драйверов после развертывания виртуальных машин серии N. Сведения об установке драйверов также доступны для [виртуальных машин Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Графический процессор](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Установка драйвера

1. Подключитесь к каждой виртуальной машине серии N с помощью удаленного рабочего стола.

2. Скачайте, извлеките и установите поддерживаемый драйвер для своей операционной системы Windows.

После установки драйвера на виртуальных машинах Azure серии NV их требуется перезагрузить. На виртуальных машинах NC перезагрузка не требуется.

## <a name="verify-driver-installation"></a>Проверка установки драйверов

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная конфигурация карты Tesla K80 на виртуальной машине Azure серии NC.

![Свойства драйвера GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Чтобы запросить состояние устройства GPU, выполните служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером.

1. Откройте командную строку и измените каталог на **:\Program Files\NVIDIA Corporation\NVSMI**.

2. Запустите `nvidia-smi`. Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит **0 %**, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине. Версия драйвера и сведения о GPU могут отличаться от показанных на изображении.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Сетевое подключение RDMA

Сетевое подключение RDMA можно включить на виртуальных машинах серии N с поддержкой RDMA, таких как NC24r, развернутых в одной группе доступности или в одном масштабируемом наборе виртуальных машин. Необходимо добавить расширение HpcVmDrivers для установки драйверов сетевых устройств Windows, обеспечивающих подключения RDMA. Чтобы в виртуальную машину серии N с поддержкой RDMA добавить расширение виртуальной машины, используйте командлеты [Azure PowerShell](/powershell/azure/overview) для Azure Resource Manager.

Чтобы установить последнюю версию расширения HpcVMDrivers 1.1 на существующую виртуальную машину myVM с поддержкой RDMA, размещенную в регионе "Западная часть США", выполните следующее.
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Дополнительные сведения см. в разделе [Обзор расширений и компонентов виртуальной машины под управлением Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) или Intel MPI 5.x. 


## <a name="next-steps"></a>Дополнительная информация

* Разработчики приложений с ускорением GPU, предназначенных для графических процессоров NVIDIA Tesla, могут также скачать и установить [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


