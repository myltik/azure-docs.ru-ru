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
ms.date: 12/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58ca4ea85b6097f7210a21db45791bb43b0e99ea
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
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

2. Запустите `nvidia-smi`. Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит **0 %**, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине. Версия драйвера и сведения о GPU могут отличаться от показанных на изображении.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Подключение к сети RDMA

Можно включить подключение к сети RDMA в N-й серии виртуальных машин с поддержкой RDMA, такие как NC24r развернуты в той же группе доступности. Необходимо добавить расширение HpcVmDrivers для установки драйверов сетевых устройств Windows, обеспечивающих подключения RDMA. Чтобы добавить расширение виртуальной Машины в виртуальной Машине N-й серии с поддержкой RDMA, используйте [Azure PowerShell](/powershell/azure/overview) командлеты для диспетчера ресурсов Azure.

> [!NOTE]
> В настоящее время только Windows Server 2012 R2 поддерживает сети RDMA на виртуальных машинах N-й серии.
> 

Чтобы установить последнюю версию расширения HpcVMDrivers 1.1 на существующую виртуальную машину myVM с поддержкой RDMA, размещенную в регионе "Западная часть США", выполните следующее.
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Дополнительные сведения см. в разделе [Обзор расширений и компонентов виртуальной машины под управлением Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) или Intel MPI 5.x. 


## <a name="next-steps"></a>Дальнейшие действия

* Можно также загрузить и установить разработчиков приложений ускорением GPU для графических процессоров NVIDIA Tesla [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


