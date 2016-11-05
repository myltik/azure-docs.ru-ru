---
title: Варианты кластера пакета HPC на основе Windows в облаке | Microsoft Docs
description: Узнайте о вариантах создания кластера высокопроизводительных вычислительных систем (HPC) на основе Windows и управления им в облаке Azure с помощью пакета Microsoft HPC.
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep

---
# Варианты создания в Azure кластера HPC под управлением Windows и управления им с помощью пакета HPC
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам создания кластеров HPC для обработки рабочих нагрузок Windows. Пакет HPC дает возможность обрабатывать также [высокопроизводительные рабочие нагрузки Linux](virtual-machines-linux-hpcpack-cluster-options.md).

## Запуск кластера пакета HPC на виртуальных машинах Azure
### Шаблоны Azure
* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Краткое руководство) [Создание кластера HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Краткое руководство) [Создание кластера HPC с помощью пользовательского образа вычислительного узла](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### Образы виртуальных машин Azure
* [Пакет HPC на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Вычислительный узел пакета HPC на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Вычислительный узел пакета HPC с Excel на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

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
* [Управление заданиями в пакете HPC](https://technet.microsoft.com/library/jj899585.aspx)

## Добавление узлов рабочей роли в кластер пакета HPC
* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)
* [Учебник: настройка гибридного кластера с пакетом HPC в Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Добавление пакетных узлов Azure в головной узел пакета HPC в Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

## Интеграция с пакетной службой Azure
* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)

## Создание кластеров RDMA для рабочих нагрузок MPI
* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->