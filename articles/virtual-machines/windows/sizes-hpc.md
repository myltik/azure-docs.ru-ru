---
title: "Размеры виртуальных машин Windows в Azure, оптимизированных для рабочих нагрузок HPC | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Windows в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии."
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: fc2cae8208baa211db2166c9d66a83153fa7b445
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2017
---
# <a name="high-performance-compute-vm-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Экземпляры с поддержкой RDMA
Подмножество экземпляров для ресурсоемких вычислений (H16r, H16mr, A8 и A9) представляет собой сетевой интерфейс для подключения с удаленным доступом к памяти (RDMA). Этот интерфейс является дополнением к стандартному сетевому интерфейсу Azure, который доступен для виртуальных машин других размеров. 
  
Этот интерфейс обеспечивает связь экземпляров с поддержкой RDMA при помощи сети InfiniBand, работающей на скорости FDR для виртуальных машин H16r и H16mr и на скорости QDR для виртуальных машин A8 и A9. Эти возможности RDMA позволяют увеличить масштабируемость и производительность приложений с интерфейсом MPI.

Ниже приведены требования к виртуальным машинам Windows с поддержкой RDMA для получения доступа к сети Azure RDMA. 

* **Операционная система**
  
  Windows Server 2012 R2, Windows Server 2012
  
  > [!NOTE]
  > Сейчас соединение RDMA в Azure не поддерживается в Windows Server 2016.
  >

* **Группа доступности или облачная служба**. Виртуальные машины с поддержкой RDMA следует развертывать в одной и той же группе доступности (при использовании модели развертывания с помощью Azure Resource Manager) или в одной и той же облачной службе (при использовании классической модели развертывания). При использовании пакетной службы Azure виртуальные машины с поддержкой RDMA должны находиться в одном пуле.

* **MPI** — Microsoft MPI (MS-MPI) 2012 R2 и более поздних версий, библиотека Intel MPI 5.x.

  Для взаимодействия между экземплярами поддерживаемые реализации MPI используют интерфейс Microsoft Network Direct. 

* **Адресное пространство сети RDMA.** Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. Чтобы выполнять приложения MPI в экземплярах, развернутых в виртуальной сети Azure, убедитесь, что адресное пространство виртуальной сети не пересекается с сетью RDMA.

* **Расширение виртуальных машин HpcVmDrivers**. На виртуальных машинах с поддержкой RDMA необходимо добавить расширение HpcVmDrivers для установки драйверов сетевых устройств Windows, обеспечивающих подключение RDMA. (В некоторых развертываниях экземпляров A8 и A9 расширение HpcVmDrivers добавляется автоматически.) Чтобы добавить в виртуальную машину расширение виртуальной машины, можно использовать командлеты [Azure PowerShell](/powershell/azure/overview). 

  
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




