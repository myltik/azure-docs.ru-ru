---
title: "Подготовка ресурсов Azure для репликации виртуальных машин Hyper-V (с System Center VMM) в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "Основные сведения о том, что нужно сделать в среде Azure перед началом репликации виртуальных машин Hyper-V (с VMM) в Azure с помощью Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 365dd9477f791432c1a92f1b81eb573dbbc6f874
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---

# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>Шаг 5. Подготовка ресурсов Azure для репликации Hyper-V (с VMM) в Azure

После проверки [требований к сети](vmm-to-azure-walkthrough-network.md) воспользуйтесь инструкциями в этой статье для подготовки ресурсов Azure, чтобы реплицировать локальные виртуальные машины Hyper-V System Center Virtual Machine Manager (VMM) в Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md).

Комментарии или вопросы технического характера можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-an-azure-account"></a>Настройка учетной записи Azure

- Получите [учетную запись Microsoft Azure](http://azure.microsoft.com/).
- Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
- Сведения о поддерживаемых регионах для Site Recovery см. в разделе "Географическая доступность" на странице [цен на службу Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ознакомьтесь с расходами [при использования Site Recovery](site-recovery-faq.md#pricing) и [сведениями о ценах](https://azure.microsoft.com/pricing/details/site-recovery/).
- Убедитесь, что учетная запись Azure имеет правильные [разрешения](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) для создания виртуальных машин Azure. [Узнайте больше](../active-directory/role-based-access-built-in-roles.md) об управлении доступом на основе ролей в Azure.


## <a name="set-up-an-azure-network"></a>Настроить сеть Azure

- Настройте [сеть Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md). Виртуальные машины Azure будут размещаться в этой сети при создании после отработки отказа.
- Сеть должна располагаться в том же регионе, что и хранилище служб восстановления.
- Служба Site Recovery на портале Azure может использовать сети, настроенные в режиме [Resource Manager](../resource-manager-deployment-model.md) или в классическом режиме.
- Рекомендуется настроить сеть перед началом работы. В противном случае это нужно будет сделать во время развертывания службы Site Recovery.
- Ознакомьтесь со сведениями о [ценах на виртуальную сеть](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Настроить учетную запись хранения Azure

- Служба Site Recovery реплицирует локальные машины в службе хранилища Azure. Виртуальные машины Azure создаются из хранилища после отработки отказа.
- Для хранения данных, реплицированных в Azure, понадобится настроить [учетную запись хранения Azure](../storage/storage-create-storage-account.md#create-a-storage-account) класса "Стандартный" или Premium.
- [Хранилище класса Premium](../storage/storage-premium-storage.md) обычно используется для виртуальных машин, которым постоянно требуется высокая производительность ввода-вывода и малая задержка для размещения интенсивных рабочих нагрузок ввода-вывода.
- Если для хранения реплицированных данных вы будете использовать учетную запись хранения класса Premium, потребуется дополнительная учетная запись хранения класса Standard для хранения журналов репликации, в которые записываются текущие изменения локальных данных.
- В зависимости от модели ресурсов, которую будут использовать виртуальные машины Azure после отработки отказа, для учетной записи необходимо настроить [режим Resource Manager](../storage/storage-create-storage-account.md) или [классический режим](../storage/storage-create-storage-account-classic-portal.md).
- Рекомендуется настроить учетную запись хранения до начала работы. В противном случае это нужно будет сделать во время развертывания службы Site Recovery. Учетные записи должны находиться в том же регионе, что и хранилище служб восстановления.
- Учетные записи хранения, используемые для Site Recovery, нельзя перемещать между группами в одной подписке или между разными подписками.


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 6. Подготовка серверов VMM и узлов Hyper-V к репликации Hyper-V в Azure](vmm-to-azure-walkthrough-vmm-hyper-v.md).

