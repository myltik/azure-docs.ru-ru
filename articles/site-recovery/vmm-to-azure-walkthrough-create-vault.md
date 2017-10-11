---
title: "Настройка хранилища для репликации Hyper-V (с System Center VMM) в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье кратко описаны шаги по настройке хранилища для репликации из Hyper-V (с VMM) в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>Шаг 7. Настройка хранилища для репликации Hyper-V

В этой статье описано, как настроить хранилище и указать, что именно необходимо реплицировать из локальной среды в Azure, используя службу [Azure Site Recovery](site-recovery-overview.md) на портале Azure.


Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Выбор целевого объекта защиты

Выберите целевые объекты и место для репликации.

1. Щелкните **Хранилища служб восстановления** > хранилище.
2. В меню ресурсов щелкните **Site Recovery** > **Подготовка инфраструктуры** > **Цель защиты**.
3. На странице **Protection goal** (Цель защиты) выберите **To Azure** (В Azure) > **Yes, with Hyper-V** (Да, с помощью Hyper-V). Выберите **Да**, чтобы подтвердить использование VMM. 

     ![Выбор цели](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Дальнейшие действия

Перейдите к разделу [Шаг 8. Настройка исходной и целевой среды для репликации физического сервера в Azure](vmm-to-azure-walkthrough-source-target.md).
