---
title: "Виртуальные машины Linux для ресурсоемких вычислений | Документация Майкрософт"
description: "Ознакомьтесь с общей информацией и рекомендациями по использованию виртуальных машин Linux серии H, а также размеров A8, A9, A10 и A11 для ресурсоемких вычислений."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a2307f7055966ec7146b5da0b4daf1ad469abe2b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Сведения об экземплярах серии H и серии A для ресурсоемких вычислений для Linux
Здесь приводятся общие сведения и некоторые рекомендации по использованию экземпляров виртуальных машин Azure новой серии H и более ранних размеров A8, A9, A10 и A11 (экземпляров для *ресурсоемких вычислений* ). Эта статья посвящена применению этих размеров на виртуальных машинах Linux. Сведения в этой статье можно также использовать для [виртуальных машин Windows](../windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Доступ к сети RDMA
Вы можете создавать кластеры виртуальных машин Linux с поддержкой RDMA, на которых выполняется один из перечисленных ниже поддерживаемых дистрибутивов Linux HPC с поддерживаемой реализацией MPI. Это позволит вам воспользоваться преимуществами сети RDMA в Azure. Сведения о развертывании и настройке см. в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **Дистрибутивы** — разверните виртуальные машины из образов SUSE Linux Enterprise Server (SLES) или Rogue Wave Software (ранее OpenLogic) CentOS HPC с поддержкой RDMA, доступных в Azure Marketplace. Соединение RDMA поддерживается в следующих образах Marketplace:
  
    * SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (Premium)
    
    * 7.1 HPC на основе CentOS, 6.5 HPC на основе CentOS.  
 
        > [!NOTE]
        > Для виртуальных машин серии H рекомендуем использовать образ SLES 12 SP1 для HPC или 7.1 HPC на основе CentOS.
        >
        > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.
        > 
        > 
* **MPI** : Intel MPI Library 5.x
  
    В зависимости от того, какой образ вы выберете на Marketplace, может потребоваться лицензирование, установка или настройка Intel MPI, как описано ниже. 
  
  * **Образ SLES 12 SP1 для HPC** — пакеты Intel MPI, размещенные на виртуальной машине. Установите , выполнив следующую команду:

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **Образы HPC на основе CentOS** — здесь уже установлена библиотека Intel MPI 5.1.  
    
    Потребуется дополнительная настройка системы для выполнения заданий MPI на кластеризованных виртуальных машинах. Например, следует установить доверительные отношения между вычислительными узлами в кластере виртуальных машин. Типичные примеры настройки можно найти в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Рекомендации по использованию пакета HPC и Linux
Для использования экземпляров для ресурсоемких вычислений в Linux можно применить [пакет Microsoft HPC](https://technet.microsoft.com/library/jj899572.aspx) (бесплатное решение по управлению кластерами и заданиями HPC). Последние выпуски пакета HPC поддерживают несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure, под управлением головного узла Windows Server. Используя вычислительные узлы Linux с поддержкой RDMA и Intel MPI, пакет HPC может планировать и выполнять приложения Linux MPI, которые обращаются к сети RDMA. Чтобы приступить к работе, см. статью [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Аспекты топологии сети
* На виртуальных машинах Linux с поддержкой RDMA интерфейс Eth1 зарезервирован для сетевого трафика RDMA. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети. Eth0 зарезервирован для обычного сетевого трафика Azure.
* В Azure не поддерживается IP через InfiniBand (IB). Поддерживается только RDMA через IB.



## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о доступности и стоимости размеров ВМ, предназначенных для ресурсоемких вычислений, вы найдете на странице с [ценами на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Чтобы приступить к развертыванию и использованию виртуальных машин Linux для ресурсоемких вычислений с поддержкой RDMA, изучите статью [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


