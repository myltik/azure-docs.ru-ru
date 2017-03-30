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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 562ebbf815f421343c580fced111cda481aa4a5c
ms.lasthandoff: 03/17/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Установка драйверов GPU для виртуальных машин серии N под управлением Windows Server
Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Windows Server 2016 или Windows Server 2012 R2, необходимо установить графические драйверы NVIDIA на каждую виртуальную машину после развертывания. Сведения об установке драйверов также доступны для [виртуальных машин Linux](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ознакомьтесь также с разделом [Общие рекомендации для виртуальных машин серии N](#general-considerations-for-n-series-vms).



## <a name="supported-gpu-drivers"></a>Поддерживаемые драйверы GPU

Подключитесь к каждой виртуальной машине серии N с помощью удаленного рабочего стола. Скачайте, извлеките и установите поддерживаемый драйвер для своей операционной системы Windows. 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>Драйверы NVIDIA Tesla для виртуальных машин NC (Tesla K80)



| ОС | Версия драйвера |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe) (EXE-файл) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe) (EXE-файл) |

> [!NOTE]
> Предоставленные в статье ссылки для скачивания драйверов Tesla актуальны на момент ее публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).
>

### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>Драйверы NVIDIA GRID для виртуальных машин NV (Tesla M60)

| ОС | Версия драйвера |
| -------- |------------- |
| Windows Server 2016 | [369.71](https://go.microsoft.com/fwlink/?linkid=836843) (ZIP-файл) |
| Windows Server 2012 R2 | [369.71](https://go.microsoft.com/fwlink/?linkid=836844) (ZIP-файл)  |



## <a name="verify-gpu-driver-installation"></a>Проверка установки драйвера GPU

После установки драйвера на виртуальных машинах Azure серии NV их требуется перезагрузить. На виртуальных машинах NC перезагрузка не требуется.

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная конфигурация карты Tesla K80 на виртуальной машине Azure серии NC.

![Свойства драйвера GPU](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

Чтобы запросить состояние устройства GPU, выполните служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

![Состояние устройства NVIDIA](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Сеть RDMA для виртуальных машин NC24r

Можно включить сетевое подключение RDMA на виртуальных машинах NC24r, развернутых в одной группе доступности. Необходимо добавить расширение HpcVmDrivers для установки драйверов сетевых устройств Windows, обеспечивающих подключения RDMA. Чтобы в виртуальную машину NC24r добавить расширение виртуальной машины, используйте командлеты [Azure PowerShell](/powershell/azureps-cmdlets-docs) для Azure Resource Manager.

> [!NOTE]
> В настоящее время только Windows Server 2012 R2 поддерживает сеть RDMA на виртуальных машинах NC24r.
> 

Чтобы установить последнюю версию расширения HpcVMDrivers 1.1 на существующую виртуальную машину myVM с поддержкой RDMA, размещенную в регионе "Западная часть США", выполните следующее.
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Дополнительные сведения см. в разделе [Обзор расширений и компонентов виртуальной машины под управлением Windows](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) или Intel MPI 5.x. 

[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).

* Разработчики приложений, использующих ускорение на GPU, которые предназначены для графических процессоров NVIDIA Tesla, могут также скачать и установить набор средств CUDA Toolkit 8 для [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) или [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).



