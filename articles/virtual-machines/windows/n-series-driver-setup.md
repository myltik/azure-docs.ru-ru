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
ms.date: 07/07/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b480d10df777a2757c073ff77e1845d33d63163a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Установка драйверов GPU для виртуальных машин серии N под управлением Windows Server
Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Windows Server 2016 или Windows Server 2012 R2, необходимо установить графические драйверы NVIDIA. В этой статье приводятся действия по установке драйверов после развертывания виртуальных машин серии N. Сведения об установке драйверов также доступны для [виртуальных машин Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

2. Запустите **nvidia-smi**. Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит **0 %**, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Сеть RDMA для виртуальных машин NC24r

Можно включить сетевое подключение RDMA на виртуальных машинах NC24r, развернутых в одной группе доступности. Необходимо добавить расширение HpcVmDrivers для установки драйверов сетевых устройств Windows, обеспечивающих подключения RDMA. Чтобы в виртуальную машину NC24r добавить расширение виртуальной машины, используйте командлеты [Azure PowerShell](/powershell/azure/overview) для Azure Resource Manager.

> [!NOTE]
> В настоящее время только Windows Server 2012 R2 поддерживает сеть RDMA на виртуальных машинах NC24r.
> 

Чтобы установить последнюю версию расширения HpcVMDrivers 1.1 на существующую виртуальную машину myVM с поддержкой RDMA, размещенную в регионе "Западная часть США", выполните следующее.
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Дополнительные сведения см. в разделе [Обзор расширений и компонентов виртуальной машины под управлением Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) или Intel MPI 5.x. 


## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).

* Разработчики приложений, использующих ускорение на GPU, которые предназначены для графических процессоров NVIDIA Tesla, могут также скачать и установить набор средств CUDA Toolkit 8 для [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) или [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


