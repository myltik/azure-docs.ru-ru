---
title: "Подготовка ресурсов Azure для репликации локальных физических серверов в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "Эта статья содержит сведения о компонентах, которые необходимо настроить в Azure, прежде чем приступить к репликации локальных физических серверов в Azure с помощью службы Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 2b277f1fb96f347cc60d1395fafb7e2707883a77
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017


---
# <a name="step-5-prepare-azure-resources-for-physical-server-replication-to-azure"></a>Шаг 5. Подготовка ресурсов Azure для репликации физического сервера в Azure


Используйте инструкции этой статьи, чтобы подготовить ресурсы Azure для репликации локальных серверов в Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md).

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Перед началом работы

Ознакомьтесь со статьей о [предварительных требованиях](physical-walkthrough-prerequisites.md).

## <a name="set-up-an-azure-account"></a>Настройка учетной записи Azure

- Получите [учетную запись Microsoft Azure](http://azure.microsoft.com/).
- Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
- Сведения о поддерживаемых регионах для Site Recovery см. в разделе **Географическая доступность** на странице [цен на службу Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ознакомьтесь с расходами [при использования Site Recovery](site-recovery-faq.md#pricing) и [сведениями о ценах](https://azure.microsoft.com/pricing/details/site-recovery/).



## <a name="set-up-an-azure-network"></a>Настроить сеть

- Настройка сети Azure. Виртуальные машины Azure будут размещаться в этой сети при создании после отработки отказа.
- Служба Site Recovery на портале Azure может использовать сети, настроенные в режиме [Resource Manager](../resource-manager-deployment-model.md) или в классическом режиме.
- Сеть должна располагаться в том же регионе, что и хранилище служб восстановления.
- Ознакомьтесь со сведениями о [ценах на виртуальную сеть](https://azure.microsoft.com/pricing/details/virtual-network/).
- Дополнительные сведения о [подключении к виртуальной машине Azure](physical-walkthrough-network.md) после отработки отказа.


## <a name="set-up-an-azure-storage-account"></a>Настроить учетную запись хранения Azure

- Служба Site Recovery реплицирует локальные сервера в службе хранилища Azure. Виртуальные машины Azure создаются из хранилища после отработки отказа.
- Настройте [учетную запись хранения Azure](../storage/storage-create-storage-account.md#create-a-storage-account) для реплицированных данных.
- Служба Site Recovery на портале Azure может использовать учетные записи хранения, настроенные в режиме Resource Manager или в классическом режиме.
- Учетная запись хранения может быть уровня "Стандартный" или [Премиум](../storage/storage-premium-storage.md).
- Если вы настроите учетную запись уровня "Премиум", вам также понадобится дополнительная учетная запись уровня "Стандартный" для данных журнала.


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Step 6: Set up a vault for physical server replication to Azure](physical-walkthrough-create-vault.md) (Шаг 6. Настройка хранилища для репликации физического сервера в Azure).

