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
ms.date: 11/18/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f3b1593a80e6eedc08005988d485a317951efd33
ms.openlocfilehash: ad2beff5bbc6aaa18247939ccfe310460f9ccdee
ms.lasthandoff: 03/01/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Сведения об экземплярах серии H и серии A для ресурсоемких вычислений для Linux
Здесь приводятся общие сведения и некоторые рекомендации по использованию экземпляров виртуальных машин Azure новой серии H и более ранних размеров A8, A9, A10 и A11 (экземпляров для *ресурсоемких вычислений* ). Эта статья посвящена применению этих размеров на виртуальных машинах Linux. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Доступ к сети RDMA
Вы можете создавать кластеры виртуальных машин Linux с поддержкой RDMA, на которых выполняется один из перечисленных ниже поддерживаемых дистрибутивов Linux HPC с поддерживаемой реализацией MPI. Это позволит вам воспользоваться преимуществами сети RDMA в Azure. Сведения о развертывании и настройке см. в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **Дистрибутивы** — разверните виртуальные машины из образов SUSE Linux Enterprise Server (SLES) или OpenLogic CentOS HPC с поддержкой RDMA, доступных в Azure Marketplace. Необходимые драйверы Linux RDMA поддерживаются только в следующих образах из Azure Marketplace:
  
  * SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (Premium)
  * SLES 12 для HPC, SLES 12 для HPC (Premium)
  * 7.1 HPC на основе CentOS
  * 6.5 HPC на основе CentOS
    
    > [!NOTE]
    > Для виртуальных машин серии H рекомендуем использовать образ SLES 12 SP1 для HPC или 7.1 HPC на основе CentOS.
    > 
    > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.
    > 
    > 
* **MPI** : Intel MPI Library 5.x
  
    В зависимости от того, какой образ вы выберете на Marketplace, может потребоваться лицензирование, установка или настройка Intel MPI, как описано ниже. 
  
  * **Образ SLES 12 SP1 для HPC** — установите пакеты Intel MPI, размещенные на виртуальной машине, выполнив следующую команду:
    
          sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  * **Образ SLES 12 для HPC** — пройдите дополнительную регистрацию, чтобы загрузить и установить Intel MPI. Инструкции см. в [документации по библиотеке Intel MPI](https://software.intel.com/en-us/intel-mpi-library/documentation).
  * **Образы HPC на основе CentOS** — здесь уже установлена библиотека Intel MPI 5.1.  
    
    Потребуется дополнительная настройка системы для выполнения заданий MPI на кластеризованных виртуальных машинах. Например, следует установить доверительные отношения между вычислительными узлами в кластере виртуальных машин. Типичные примеры настройки можно найти в статье [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Рекомендации по использованию пакета HPC и Linux
Для использования экземпляров для ресурсоемких вычислений в Linux можно применить [пакет Microsoft HPC](https://technet.microsoft.com/library/jj899572.aspx) (бесплатное решение по управлению кластерами и заданиями HPC). Последние выпуски пакета HPC 2012 R2 поддерживают несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure, под управлением головного узла Windows Server. Используя вычислительные узлы Linux с поддержкой RDMA и Intel MPI, пакет HPC может планировать и выполнять приложения Linux MPI, которые обращаются к сети RDMA. Чтобы приступить к работе, см. статью [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Аспекты топологии сети
* На виртуальных машинах Linux с поддержкой RDMA интерфейс Eth1 зарезервирован для сетевого трафика RDMA. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети. Eth0 зарезервирован для обычного сетевого трафика Azure.
* В Azure не поддерживается IP через InfiniBand (IB). Поддерживается только RDMA через IB.

## <a name="rdma-driver-updates-for-sles-12"></a>Обновления драйверов RDMA для SLES 12
После создания виртуальной машины на основе образа SLES 12 HPC может потребоваться обновить драйверы RDMA на виртуальных машинах для подключения к сети RDMA. 

> [!IMPORTANT]
> Это **обязательный** шаг для развертывания виртуальных машин SLES 12 HPC во всех регионах Azure. Этот шаг не требуется, если вы развертываете образ SLES 12 SP1 для HPC, 7.1 HPC на основе CentOS или 6.5 HPC на основе CentOS. 
> 
> 

Прежде чем обновлять драйверы, остановите все процессы **zypper** или другие процессы, блокирующие базы данных репозиториев SUSE на виртуальной машине. В противном случае драйверы могут не обновиться должным образом.  

Чтобы обновить драйверы Linux RDMA на каждой виртуальной машине, выполните на клиентском компьютере один из следующих наборов команд Azure CLI.

**Виртуальная машина SLES 12 для HPC, подготовленная с помощью классической модели развертывания**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Виртуальная машина SLES 12 для HPC, подготовленная с помощью модели развертывания Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

> [!NOTE]
> Установка драйверов может занять определенное время. Команда завершится без выходных данных. После обновления виртуальная машина перезапустится и через несколько минут будет готова к работе.
> 
> 

### <a name="sample-script-for-driver-updates"></a>Пример сценария для обновления драйверов
Если вы используете кластер виртуальных машин SLES 12 для HPC, обновление драйверов на всех узлах можно внести в сценарий. Например, следующий сценарий обновляет драйверы на 8 узлах кластера.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о доступности и стоимости размеров ВМ, предназначенных для ресурсоемких вычислений, вы найдете на странице с [ценами на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин в Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Чтобы приступить к развертыванию и использованию виртуальных машин Linux для ресурсоемких вычислений с поддержкой RDMA, изучите статью [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


