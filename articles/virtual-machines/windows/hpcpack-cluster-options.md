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
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: c5b8c16e076be3002425ceeac377043cea1a40a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Варианты создания и администрирования кластера HPC для рабочих нагрузок Windows с помощью пакета HPC в Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Эта статья посвящена вариантам создания кластеров HPC для обработки рабочих нагрузок Windows. Кластеры пакетов HPC дают возможность обрабатывать также [рабочие нагрузки HPC для Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Кластер пакета HPC на виртуальных машинах Azure и масштабируемые наборы виртуальных машин
### <a name="azure-templates"></a>Шаблоны Azure
* (GitHub) [Шаблоны кластера пакета HPC 2016](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Шаблоны кластера пакета HPC 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Кластер пакета HPC для рабочих нагрузок Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Краткое руководство) [Создание кластера HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Краткое руководство) [Создание кластера HPC с помощью пользовательского образа вычислительного узла](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Образы виртуальных машин Azure
* [Головной узел пакета HPC 2016 на Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Вычислительный узел пакета HPC 2016 на Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Головной узел пакета HPC 2016 на Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Вычислительный узел пакета HPC 2016 на Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Головной узел пакета HPC 2012 R2 на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Вычислительный узел пакета HPC 2012 R2 на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Вычислительный узел пакета HPC с Excel на Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Скрипт развертывания PowerShell для пакета HPC 2012 R2
* [Создание кластера для высокопроизводительных вычислений (HPC) Windows с помощью сценария развертывания пакета HPC в IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Руководства
* [Руководство: Развертывание кластера пакета HPC 2016 в Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Приступая к работе с рабочими нагрузками Excel и SOA в кластере пакета HPC в Azure](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Развертывание вручную с помощью портала Azure
* [Создание головного узла кластера пакета HPC на виртуальной машине Azure с помощью образа Marketplace](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Управление кластерами
* [Управление вычислительными кластерами пакета HPC в Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Автоматическое изменение размера ресурсов кластера пакета HPC в Azure в соответствии с рабочей нагрузкой кластера](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Отправка заданий в кластер пакета HPC в Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Управление заданиями в пакете HPC](https://technet.microsoft.com/library/jj899585.aspx)
* [Управление кластером пакета HPC 2016 в Azure с помощью Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>Ускорение с помощью узлов рабочих ролей 
* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)
* [Учебник: настройка гибридного кластера с пакетом HPC в Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Добавление "расширительных" узлов по запросу в кластер пакета HPC в Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Ускорение с помощью пакетной службы Azure
* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Кластеры RDMA для рабочих нагрузок MPI
* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

