<properties
 pageTitle="Варианты кластеров пакета HPC в облаке | Microsoft Azure"
 description="Сведения о вариантах создания кластеров высокопроизводительных вычислений (HPC) и управления ими в облаке Azure с помощью пакета Microsoft HPC."
 services="virtual-machines,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/08/2015"
 ms.author="danlep"/>

# Варианты создания кластера высокопроизводительных вычислений (HPC) в Azure и управления им с помощью пакета Microsoft HPC

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Используйте преимущества пакета Microsoft HPC и служб вычислений и инфраструктуры Azure для создания кластера высокопроизводительных вычислений (HPC) и управления им. [Пакет HPC](https://technet.microsoft.com/library/jj899572.aspx) — это бесплатное решение Майкрософт для высокопроизводительных вычислений, созданное на основе технологий Microsoft Azure и Windows Server, которое поддерживает рабочие нагрузки HPC как в Windows, так и в Linux. Облачный кластер пакета HPC предоставляет администратору кластера или независимому поставщику ПО (ISV) гибкую, масштабируемую платформу для запуска приложений с большим количеством вычислений, сокращая инвестиции в инфраструктуру локального вычислительного кластера.


## Запуск кластера пакета HPC на виртуальных машинах Azure


### Образы виртуальных машин Azure

* [Пакет HPC на Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Вычислительный узел пакета HPC на Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Вычислительный узел пакета HPC с Excel на Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### Шаблоны быстрого запуска Azure

* [Создание кластера HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* [Создание кластера пакета HPC с вычислительными узлами Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [Создание кластера HPC с пользовательским образом вычислительного узла](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Сценарий развертывания PowerShell

* [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](virtual-machines-hpcpack-cluster-powershell-script.md)

### Учебники

* [Учебник: начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

* [Учебник. Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

* [Учебник: начало работы с кластером пакета HPC в Azure для запуска рабочих нагрузок Excel и SOA](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)



### Развертывание вручную с помощью портала Azure



* [Настройка головного узла кластера пакета HPC на виртуальной машине Azure](virtual-machines-hpcpack-cluster-headnode.md)

### Управление кластерами

* [Управление вычислительными кластерами пакета HPC в Azure](virtual-machines-hpcpack-cluster-node-manage.md)

* [Добавление пакетных узлов Azure в головной узел пакета HPC в Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Увеличение и сжатие вычислительных ресурсов Azure в кластере пакета HPC](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Отправка заданий в кластер пакета HPC в Azure](virtual-machines-hpcpack-cluster-submit-jobs.md)


## Добавление узлов рабочей роли в кластер пакета HPC


* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)

* [Учебник: настройка гибридного кластера с пакетом HPC в Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Добавление пакетных узлов Azure в головной узел пакета HPC в Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Увеличение и сжатие вычислительных ресурсов Azure в кластере пакета HPC](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

## Интеграция с пакетной службой Azure 

* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)

## Создание кластеров RDMA для рабочих нагрузок MPI

* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-hpcpack-cluster-rdma.md)

* [Учебник: выполнение заданий OpenFOAM с помощью пакета Microsoft HPC в кластере Linux RDMA в Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=AcomDC_1125_2015-->