<properties
 pageTitle="Варианты кластеров пакета HPC на основе Linux в облаке | Microsoft Azure"
 description="Узнайте о вариантах создания кластера высокопроизводительных вычислительных систем (HPC) на основе Linux и управления им в облаке Azure с помощью пакета Microsoft HPC."
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/29/2016"
 ms.author="danlep"/>

# Возможности создания кластера высокопроизводительных вычислений (HPC) в Azure и управления им с помощью пакета Microsoft HPC

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам использования пакета HPC для запуска рабочих нагрузок Linux. Существуют также возможности запуска [рабочих нагрузок HPC Windows с помощью пакета HPC](virtual-machines-windows-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Запуск кластера пакета HPC на виртуальных машинах Azure

### Шаблоны Azure


* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Краткое руководство) [Создание кластера HPC с вычислительными узлами Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)


### Образы виртуальных машин Azure

* [Пакет HPC на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### Сценарий развертывания PowerShell

* [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Учебники

* [Учебник: начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Учебник. Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Учебник: выполнение заданий OpenFOAM с помощью пакета Microsoft HPC в кластере Linux RDMA в Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Учебник. Выполнение заданий STAR-CCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Управление кластерами

* [Отправка заданий в кластер пакета HPC в Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Создание кластеров RDMA для рабочих нагрузок MPI

* [Учебник: выполнение заданий OpenFOAM с помощью пакета Microsoft HPC в кластере Linux RDMA в Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0504_2016-->