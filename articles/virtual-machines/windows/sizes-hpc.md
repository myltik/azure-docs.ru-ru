---
title: Размеры виртуальных машин Windows в Azure, оптимизированных для рабочих нагрузок HPC | Документация Майкрософт
description: Список различных размеров виртуальных машин Windows в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: e402fd3ac95cac4816b9442f7c08aeaf7c108886
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="high-performance-compute-vm-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Операционная система**. Windows Server 2016, Windows Server 2012 R2, Windows Server 2012.

* **MPI** — Microsoft MPI (MS-MPI) 2012 R2 и более поздних версий, библиотека Intel MPI 5.x.

  Для взаимодействия между экземплярами поддерживаемые реализации MPI используют интерфейс Microsoft Network Direct. 

* **Адресное пространство сети RDMA.** Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. Чтобы выполнять приложения MPI в экземплярах, развернутых в виртуальной сети Azure, убедитесь, что адресное пространство виртуальной сети не пересекается с сетью RDMA.

* **Расширение виртуальных машин HpcVmDrivers**. На виртуальных машинах с поддержкой RDMA следует добавить расширение HpcVmDrivers для установки драйверов сетевых устройств Windows, обеспечивающих подключение RDMA. (В некоторых развертываниях экземпляров A8 и A9 расширение HpcVmDrivers добавляется автоматически.) Чтобы добавить в виртуальную машину расширение виртуальной машины, можно использовать командлеты [Azure PowerShell](/powershell/azure/overview). 

  
  Следующая команда устанавливает последнюю версию (1.1) расширения HpcVMDrivers на существующую виртуальную машину с поддержкой RDMA, которая носит имя *myVM*. Эта машина развернута в группе ресурсов с именем *myResourceGroup* в регионе *Западная часть США*:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Дополнительные сведения см. в статье [Обзор расширений и компонентов виртуальной машины под управлением Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Вы также можете работать с расширениями для виртуальных машин, развернутых в рамках [классической модели развертывания](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Использование пакета HPC

[Пакет Microsoft HPC](https://technet.microsoft.com/library/jj899572.aspx). Бесплатный кластер высокопроизводительных вычислений Майкрософт и решение для управления заданиями — это один из вариантов создания вычислительного кластера в Azure для выполнения приложений MPI под управлением Windows и других рабочих нагрузок HPC. Пакет HPC 2012 R2 и более поздних версий включает среду выполнения для MS-MPI, которая использует сеть Azure RDMA при развертывании на виртуальных машинах с поддержкой RDMA.



## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](../virtual-machines-windows-sizes-memory.md)
- [Оптимизированные для хранилища](../virtual-machines-windows-sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)

## <a name="next-steps"></a>Дополнительная информация

- Контрольные списки для использования экземпляров с большим объемом вычислений с пакетом HPC на Windows Server см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Сведения об использовании экземпляров для ресурсоемких вычислений при запуске приложений MPI с использованием пакетной службы Azure см. в статье [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе](../../batch/batch-mpi.md).

- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.




