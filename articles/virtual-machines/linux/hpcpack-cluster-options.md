---
title: "Варианты создания кластера пакета HPC на основе Linux в Azure | Документация Майкрософт"
description: "Узнайте о вариантах создания кластера высокопроизводительных вычислительных систем (HPC) на основе Linux и управления им в облаке Azure с помощью пакета Microsoft HPC."
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 08/25/2017
ms.author: danlep
ms.openlocfilehash: 99aee6b73242d21842b02ded8c9e4e25bc5cedb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Варианты создания и администрирования кластера HPC для рабочих нагрузок Linux с помощью пакета HPC в Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам использования пакета HPC для запуска рабочих нагрузок Linux. Существуют также возможности запуска [рабочих нагрузок HPC Windows с помощью пакета HPC](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms"></a>Кластер пакета HPC на виртуальных машинах Azure
### <a name="azure-templates"></a>Шаблоны Azure
* (GitHub) [Шаблоны кластера пакета HPC 2016](https://github.com/MsHpcPack/HPCPack2016)
* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Краткое руководство) [Создание кластера HPC с вычислительными узлами Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Скрипт развертывания PowerShell для пакета HPC 2012 R2
* [Создание кластера HPC Linux с помощью сценария развертывания IaaS пакета HPC](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Учебники
* [Руководство. Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. выполнение заданий OpenFOAM с помощью пакета Microsoft HPC в кластере Linux RDMA в Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. Выполнение заданий STAR-CCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>Управление кластерами
* [Отправка заданий в кластер пакета HPC в Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Управление заданиями в пакете HPC](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Кластеры RDMA для рабочих нагрузок MPI
* [Руководство. выполнение заданий OpenFOAM с помощью пакета Microsoft HPC в кластере Linux RDMA в Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

