<properties
 pageTitle="Варианты кластера пакета HPC на основе Windows в облаке | Microsoft Azure"
 description="Узнайте о вариантах создания кластера высокопроизводительных вычислительных систем (HPC) на основе Windows и управления им в облаке Azure с помощью пакета Microsoft HPC."
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="04/29/2016"
 ms.author="danlep"/>

# Возможности создания кластера высокопроизводительных вычислительных систем (HPC) на основе Windows и управления им в Azure с помощью пакета Microsoft HPC

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам использования пакета HPC для запуска рабочих нагрузок Windows. Существуют также возможности запуска [рабочих нагрузок HPC Linux с помощью пакета HPC](virtual-machines-linux-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Запуск кластера пакета HPC на виртуальных машинах Azure

### Шаблоны Azure

* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Краткое руководство) [Создание кластера HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* (Краткое руководство) [Создание кластера HPC с пользовательским образом вычислительного узла](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Образы виртуальных машин Azure

* [Пакет HPC на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Вычислительный узел пакета HPC на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Вычислительный узел пакета HPC с Excel на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### Сценарий развертывания PowerShell

* [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### Учебники

* [Учебник: начало работы с кластером пакета HPC в Azure для запуска рабочих нагрузок Excel и SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### Развертывание вручную с помощью портала Azure

* [Настройка головного узла кластера пакета HPC на виртуальной машине Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### Управление кластерами

* [Управление вычислительными кластерами пакета HPC в Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)


* [Увеличение и сжатие вычислительных ресурсов Azure в кластере пакета HPC](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Отправка заданий в кластер пакета HPC в Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Добавление узлов рабочей роли в кластер пакета HPC


* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)

* [Учебник: настройка гибридного кластера с пакетом HPC в Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Добавление пакетных узлов Azure в головной узел пакета HPC в Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

* [Увеличение и сжатие вычислительных ресурсов Azure в кластере пакета HPC](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

## Интеграция с пакетной службой Azure 

* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)

## Создание кластеров RDMA для рабочих нагрузок MPI

* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0504_2016-->