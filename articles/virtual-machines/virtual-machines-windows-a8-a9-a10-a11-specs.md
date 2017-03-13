---
title: "О виртуальных машинах Windows для ресурсоемких вычислений | Документация Майкрософт"
description: "Ознакомьтесь с основными сведениями и рекомендациями по использованию серии Azure H и размеров A8, A9, A10 и A11, предназначенных для ресурсоемких вычислений, на виртуальных машинах Windows и в облачных службах."
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 58e8474a9cafdad06c2968a7317e0c30474b5069
ms.openlocfilehash: 5021a0aa554978fbb5543024400986715227de0b
ms.lasthandoff: 03/01/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>Сведения об экземплярах серии H и серии A для ресурсоемких вычислений для Windows
Здесь приводятся общие сведения и некоторые рекомендации по использованию экземпляров виртуальных машин Azure новой серии H и более ранних экземпляров A8, A9, A10 и A11 (экземпляров для *ресурсоемких вычислений* ). Эта статья посвящена использованию таких экземпляров для виртуальных машин Windows. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Доступ к сети RDMA
Чтобы трафик Windows MPI получил доступ к сети Azure RDMA, экземпляры с поддержкой RDMA должны соответствовать следующим требованиям. 

* **Операционная система**
  
  * **Виртуальные машины** — Windows Server 2012 R2, Windows Server 2012.
  * **Облачные службы** — Windows Server 2012 R2, Windows Server 2012 или семейство гостевых ОС Windows Server 2008 R2.
* **MPI** — Microsoft MPI (MS-MPI) 2012 R2 и более поздних версий, библиотека Intel MPI 5.x.

  Для взаимодействия между экземплярами поддерживаемые реализации MPI используют интерфейс Microsoft Network Direct. 
* **Расширение виртуальных машин HpcVmDrivers**. На виртуальных машинах с поддержкой RDMA необходимо добавить расширение HpcVmDrivers для установки драйверов устройств сети Windows, обеспечивающих подключения RDMA. (В некоторых развертываниях экземпляров A8 и A9 расширение HpcVmDrivers добавляется автоматически.) Если в виртуальную машину требуется добавить расширение виртуальной машины, можно использовать командлеты [Azure PowerShell](/powershell/azureps-cmdlets-docs) для Azure Resource Manager.

  Дополнительные сведения о последних версиях расширения HpcVmDrivers см. здесь:

  ```PowerShell
  Get-AzureVMAvailableExtension -ExtensionName  "HpcVmDrivers"
  ```

  Чтобы установить последнюю версию расширения HpcVMDrivers 1.1 в существующей виртуальной машине myVM с поддержкой RDMA, следуйте перечисленным далее инструкциям.
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Дополнительные сведения см. в статье [Управление расширениями виртуальной машины](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). С расширениями для виртуальных машин можно также работать в рамках [классической модели развертывания](virtual-machines-windows-classic-manage-extensions.md).


## <a name="considerations-for-hpc-pack-and-windows"></a>Рекомендации по использованию пакета HPC и Windows
Для использования экземпляров для ресурсоемких вычислений в Windows Server наличие [пакета Microsoft HPC](https://technet.microsoft.com/library/jj899572.aspx) (бесплатное решение по управлению кластерами и заданиями HPC) не обязательно. Однако это один из вариантов создания вычислительного кластера в Azure для выполнения приложений MPI под управлением Windows и других рабочих нагрузок HPC. Пакет HPC 2012 R2 и более поздних версий включает среду выполнения для MS-MPI, которая может использовать сеть Azure RDMA при развертывании на виртуальных машинах с поддержкой RDMA.

Дополнительные сведения и контрольные списки для использования экземпляров с большим объемом вычислений с пакетом HPC на Windows Server см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о доступности и стоимости размеров ВМ, предназначенных для ресурсоемких вычислений, см. на страницах с [ценами на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) и [облачные службы](https://azure.microsoft.com/pricing/details/cloud-services/).
* Сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Чтобы приступить к развертыванию и использованию экземпляров для ресурсоемких вычислений с помощью пакета HPC в Windows, см. статью [Настройка кластера Windows RDMA с пакетом HPC Pack и экземплярами A8 и A9 для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Дополнительные сведения об использовании экземпляров для ресурсоемких вычислений для выполнения приложений MPI с использованием пакетной службы Azure см. в статье [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе Azure](../batch/batch-mpi.md).


