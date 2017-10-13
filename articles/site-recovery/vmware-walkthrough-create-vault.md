---
title: "Настройка хранилища для репликации из VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье кратко описаны шаги для настройки хранилища для репликации из VMware в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8bce940e-f19f-4418-8360-aee7b073519a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: dca95ad46b8de587140c3573ba6ed5702a122032
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-vmware-replication-to-azure"></a>Шаг 7. Настройка хранилища для репликации из VMware в Azure


В этой статье описано, как настроить хранилище и указать, что именно необходимо реплицировать из локальной среды в Azure, используя службу [Azure Site Recovery](site-recovery-overview.md) на портале Azure.


Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Выбор целевого объекта защиты

Выберите целевые объекты и место для репликации.

1. Щелкните **Хранилища служб восстановления** > хранилище.
2. В меню ресурсов щелкните **Site Recovery** > **Подготовка инфраструктуры** > **Цель защиты**.
3. На странице **Цель защиты** выберите **В Azure** > **Да, с использованием низкоуровневой оболочки VMware vSphere**.



## <a name="next-steps"></a>Дальнейшие действия

Перейдите к разделу [Шаг 8. Настройка исходной и целевой среды для репликации физического сервера в Azure](vmware-walkthrough-source-target.md).
