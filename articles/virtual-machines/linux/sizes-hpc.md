---
title: "Размеры виртуальных машин Linux в Azure, оптимизированных для HPC | Документация Майкрософт"
description: "Список различных размеров виртуальных машин Linux в Azure, оптимизированных для высокопроизводительных вычислений."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 31a1ac1f58f52abd2d62e95a2de1a42ce678c43c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Экземпляры с поддержкой RDMA
Подмножество экземпляров для ресурсоемких вычислений (H16r, H16mr, A8 и A9) представляет собой сетевой интерфейс для подключения с удаленным доступом к памяти (RDMA). Этот интерфейс является дополнением к стандартному сетевому интерфейсу Azure, который доступен для виртуальных машин других размеров. 
  
Этот интерфейс обеспечивает связь экземпляров с поддержкой RDMA при помощи сети InfiniBand, работающей на скорости FDR для виртуальных машин H16r и H16mr и на скорости QDR для виртуальных машин A8 и A9. Эти возможности RDMA позволяют увеличить масштабируемость и производительность приложений с интерфейсом MPI, выполняемых в Intel MPI версии 5.x и выше.

Виртуальные машины с поддержкой RDMA следует развертывать в одной и той же группе доступности (при использовании модели развертывания с помощью Azure Resource Manager) или в одной и той же облачной службе (при использовании классической модели развертывания). Ниже приведены дополнительные требования к виртуальным машинам Linux с поддержкой RDMA для получения доступа к сети Azure RDMA.

### <a name="distributions"></a>Дистрибутивы
 
Разверните виртуальные машины для ресурсоемких вычислений из одного из образов с поддержкой подключения RDMA в Azure Marketplace:
  
* **Ubuntu** — Ubuntu Server 16.04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** — SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (уровень "Премиум"), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (уровень "Премиум"). Драйверы RDMA и пакеты Intel MPI будут установлены на виртуальной машине. Установите MPI, выполнив следующую команду:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC на базе CentOS** - HPC на базе CentOS 7.3, HPC на базе CentOS 7.1, HPC на базе CentOS 6.8 или HPC на базе CentOS 6.5 (для серии H рекомендуется использовать версию 7.1 и выше). Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.  
 
  > [!NOTE]
  > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.
  > 
 
### <a name="cluster-configuration"></a>Конфигурация кластера 
    
Потребуется дополнительная настройка системы для выполнения заданий MPI на кластеризованных виртуальных машинах. Например, следует установить доверительные отношения между вычислительными узлами в кластере виртуальных машин. Типичные примеры настройки можно найти в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Аспекты топологии сети
* На виртуальных машинах Linux с поддержкой RDMA интерфейс Eth1 зарезервирован для сетевого трафика RDMA. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети. Eth0 зарезервирован для обычного сетевого трафика Azure.

* В Azure не поддерживается IP через InfiniBand (IB). Поддерживается только RDMA через IB.

## <a name="using-hpc-pack"></a>Использование пакета HPC
Для использования экземпляров для ресурсоемких вычислений в Linux можно применить [пакет Microsoft HPC](https://technet.microsoft.com/library/jj899572.aspx) (бесплатное решение по управлению кластерами и заданиями HPC). Последние выпуски пакета HPC поддерживают несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure, под управлением головного узла Windows Server. Используя вычислительные узлы Linux с поддержкой RDMA и Intel MPI, пакет HPC может планировать и выполнять приложения Linux MPI, которые обращаются к сети RDMA. Ознакомьтесь со статьей [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к развертыванию и использованию виртуальных машин Linux для ресурсоемких вычислений с поддержкой RDMA, изучите статью [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.




