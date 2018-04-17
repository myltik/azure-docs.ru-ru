---
title: Размеры виртуальных машин Linux в Azure, оптимизированных для HPC | Документация Майкрософт
description: Список различных размеров виртуальных машин Linux в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: a24cb03cd30b212650a36cd5ac40977de5eea11e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Поддерживаются только версии Intel MPI 5.x. Более поздние версии библиотеки среды выполнения Intel MPI (2017, 2018) несовместимы с драйверами Azure Linux RDMA.


### <a name="distributions"></a>Дистрибутивы
 
Разверните виртуальные машины для ресурсоемких вычислений из одного из образов с поддержкой подключения RDMA в Azure Marketplace:
  
* **Ubuntu** — Ubuntu Server 16.04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** — SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (уровень "Премиум"), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (уровень "Премиум"). Драйверы RDMA и пакеты Intel MPI будут установлены на виртуальной машине. Установите MPI, выполнив следующую команду:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **На основе centOS HPC** — на основе CentOS HPC 6.5 или более поздней версии (для серии H рекомендуется 7.1 или более поздняя версия). Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.  
 
  > [!NOTE]
  > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.
  > 
 
### <a name="cluster-configuration"></a>Конфигурация кластера 
    
Потребуется дополнительная настройка системы для выполнения заданий MPI на кластеризованных виртуальных машинах. Например, следует установить доверительные отношения между вычислительными узлами в кластере виртуальных машин. Типичные примеры настройки можно найти в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Аспекты топологии сети
* На виртуальных машинах Linux с поддержкой RDMA интерфейс Eth1 зарезервирован для сетевого трафика RDMA. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети. Eth0 зарезервирован для обычного сетевого трафика Azure.

* Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. 


## <a name="using-hpc-pack"></a>Использование пакета HPC
Для использования экземпляров для ресурсоемких вычислений в Linux можно применить [пакет Microsoft HPC](https://technet.microsoft.com/library/jj899572.aspx) (бесплатное решение по управлению кластерами и заданиями HPC). Последние выпуски пакета HPC поддерживают несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure, под управлением головного узла Windows Server. Используя вычислительные узлы Linux с поддержкой RDMA и Intel MPI, пакет HPC может планировать и выполнять приложения Linux MPI, которые обращаются к сети RDMA. Ознакомьтесь со статьей [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Дополнительная информация

- Чтобы приступить к развертыванию и использованию виртуальных машин Linux для ресурсоемких вычислений с поддержкой RDMA, изучите статью [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.




