---
title: Варианты создания кластера пакета HPC на основе Linux в Azure | Документация Майкрософт
description: Узнайте о вариантах создания кластера высокопроизводительных вычислительных систем (HPC) на основе Linux и управления им в облаке Azure с помощью пакета Microsoft HPC.
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166460"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Варианты создания и администрирования кластера HPC для рабочих нагрузок Linux с помощью пакета HPC в Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам использования пакета HPC для запуска рабочих нагрузок Linux в Azure. Существуют также возможности запуска [рабочих нагрузок HPC Windows с помощью пакета HPC](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Кластер пакета HPC на виртуальных машинах Azure и масштабируемые наборы виртуальных машин
### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager
* (GitHub) [Шаблоны кластера пакета HPC 2016 с обновлением 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Шаблоны кластера пакета HPC 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Краткое руководство) [Создание кластера HPC 2012 R2 с вычислительными узлами Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Образы виртуальных машин Azure
Просмотрите [образы пакета HPC в Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22), если вы хотите создать кластер пакета HPC в Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Кластер пакета HPC 2012 R2 в классической модели развертывания
* [Создание кластера HPC Linux с помощью сценария развертывания IaaS пакета HPC](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Настройка кластера Linux RDMA для выполнения приложений MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. выполнение заданий OpenFOAM с помощью пакета Microsoft HPC в кластере Linux RDMA в Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Руководство. Выполнение заданий STAR-CCM+ в кластере Linux RDMA в Azure с помощью пакета Microsoft HPC](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Отправка заданий
* [Отправка заданий в кластер пакета HPC в Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




