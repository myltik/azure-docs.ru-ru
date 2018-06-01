---
title: Варианты создания кластера пакета HPC на основе Windows в Azure | Документация Майкрософт
description: Узнайте о вариантах создания кластера высокопроизводительных вычислительных систем (HPC) на основе Windows и управления им в облаке Azure с помощью пакета Microsoft HPC.
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165763"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Варианты создания и администрирования кластера HPC для рабочих нагрузок Windows с помощью пакета HPC в Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам создания в Azure кластеров HPC для выполнения рабочих нагрузок Windows. Кластеры пакетов HPC дают возможность обрабатывать также [рабочие нагрузки HPC для Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Кластер пакета HPC на виртуальных машинах Azure и масштабируемые наборы виртуальных машин
### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager
* (GitHub) [Шаблоны кластера пакета HPC 2016 с обновлением 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Шаблоны кластера пакета HPC 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Краткое руководство) [Создание кластера пакета HPC 2012 R2](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Краткое руководство) [Создание кластера пакета HPC 2012 R2 с помощью пользовательского образа вычислительного узла](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Образы виртуальных машин Azure
Просмотрите [образы пакета HPC в Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22), если вы хотите создать кластер пакета HPC в Azure.


### <a name="tutorials"></a>Учебники
* [Руководство: Развертывание кластера пакета HPC 2016 в Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Управление кластером пакета HPC 2016 в Azure с помощью Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>Развертывание кластера пакета HPC 2012 R2 в классической модели развертывания
* [Создание кластера для высокопроизводительных вычислений (HPC) Windows с помощью сценария развертывания пакета HPC в IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Приступая к работе с рабочими нагрузками Excel и SOA в кластере пакета HPC в Azure](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Управление вычислительными кластерами пакета HPC в Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Автоматическое изменение размера ресурсов кластера пакета HPC в Azure в соответствии с рабочей нагрузкой кластера](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Повышение производительности в Azure с помощью пакета HPC 2012 R2
* [Burst to Azure Worker Instances with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Повышение производительности рабочих экземпляров Azure с помощью пакета Microsoft HPC)
* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)
* [Учебник: настройка гибридного кластера с пакетом HPC в Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Отправка заданий

* [Отправка заданий в кластер пакета HPC в Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






