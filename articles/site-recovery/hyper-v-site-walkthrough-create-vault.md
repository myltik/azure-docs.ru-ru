---
title: "Настройка хранилища для репликации Hyper-V (без System Center VMM) в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье кратко описаны шаги для настройки хранилища для репликации из Hyper-V в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017

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
3. На странице **Protection goal** (Цель защиты) выберите **To Azure** (В Azure) > **Yes, with Hyper-V** (Да, с помощью Hyper-V). Выберите **Нет** , чтобы подтвердить, что VMM не используется. 

    ![Выбор цели](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>Дальнейшие действия

Перейдите к разделу [Шаг 8. Настройка исходной и целевой среды для репликации физического сервера в Azure](hyper-v-site-walkthrough-source-target.md).

