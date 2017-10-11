---
title: "Подготовка ресурсов Azure для репликации виртуальных машин Hyper-V (без System Center VMM) в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "Основные сведения о том, что нужно сделать в среде Azure перед началом репликации виртуальных машин Hyper-V (без VMM) в Azure с помощью Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 28fa722c-675e-4637-98eb-7ccbf3806d69
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: 1a30cadaab7e053184f0be133f1da5bfddc1fd91
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-to-azure"></a>Шаг 5. Подготовка ресурсов Azure для репликации Hyper-V в Azure

Воспользуйтесь инструкциями из этой статьи, чтобы с помощью службы [Azure Site Recovery](site-recovery-overview.md) подготовить ресурсы Azure для репликации локальных виртуальных машин Hyper-V (без в System Center VMM) Azure.

Комментарии или вопросы технического характера можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Перед началом работы

Ознакомьтесь со статьей о [предварительных требованиях](hyper-v-site-walkthrough-prerequisites.md).

## <a name="set-up-an-azure-account"></a>Настройка учетной записи Azure

- Получите [учетную запись Microsoft Azure](http://azure.microsoft.com/).
- Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
- Сведения о поддерживаемых регионах для Site Recovery см. в разделе "Географическая доступность" на странице [цен на службу Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ознакомьтесь с расходами [при использования Site Recovery](site-recovery-faq.md#pricing) и [сведениями о ценах](https://azure.microsoft.com/pricing/details/site-recovery/).


## <a name="set-up-an-azure-network"></a>Настроить сеть

- Настройка сети Azure. Виртуальные машины Azure будут размещаться в этой сети при создании после отработки отказа.
- Сеть должна располагаться в том же регионе, что и хранилище служб восстановления.
- Служба Site Recovery на портале Azure может использовать сети, настроенные в режиме [Resource Manager](../resource-manager-deployment-model.md) или в классическом режиме.
- Рекомендуется настроить сеть перед началом работы. В противном случае это нужно будет сделать во время развертывания службы Site Recovery.
- Ознакомьтесь со сведениями о [ценах на виртуальную сеть](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Настроить учетную запись хранения Azure

- Служба Site Recovery реплицирует локальные машины в службе хранилища Azure. Виртуальные машины Azure создаются из хранилища после отработки отказа.
- Для хранения данных, реплицированных в Azure, понадобится настроить [учетную запись хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) класса "Стандартный" или Premium.
- [Хранилище класса Premium](../storage/common/storage-premium-storage.md) обычно используется для виртуальных машин, которым постоянно требуется высокая производительность ввода-вывода и малая задержка для размещения интенсивных рабочих нагрузок ввода-вывода.
- Если для хранения реплицированных данных вы будете использовать учетную запись хранения класса Premium, потребуется дополнительная учетная запись хранения класса Standard для хранения журналов репликации, в которые записываются текущие изменения локальных данных.
- В зависимости от модели ресурсов, которую будут использовать виртуальные машины Azure после отработки отказа, для учетной записи необходимо настроить [режим Resource Manager](../storage/common/storage-create-storage-account.md) или [классический режим](../storage/common/storage-create-storage-account.md).
- Рекомендуется настроить учетную запись хранения до начала работы. В противном случае это нужно будет сделать во время развертывания службы Site Recovery. Учетные записи должны находиться в том же регионе, что и хранилище служб восстановления.
- Учетные записи хранения, используемые для Site Recovery, нельзя перемещать между группами в одной подписке или между разными подписками.


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Step 6: Prepare on-premises VMware replication to Azure](hyper-v-site-walkthrough-prepare-hyper-v.md) (Шаг 6. Подготовка ресурсов Hyper-V).
