---
title: "Настройка хранилища для репликации физического сервера в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье кратко описаны шаги настройки хранилища для репликации из физических серверов в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017


---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>Шаг 6. Настройка хранилища для репликации физического сервера в Azure


В этой статье описывается настройка хранилища. Вы создадите хранилище и укажете, что именно необходимо реплицировать из локальной среды в Azure, используя службу [Azure Site Recovery](site-recovery-overview.md) на портале Azure.


Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Выбор целевого объекта защиты

Выберите целевые объекты и место для репликации.

1. Щелкните **Хранилища служб восстановления** > хранилище.
2. В меню ресурсов щелкните **Site Recovery** > **Подготовка инфраструктуры** > **Цель защиты**.
3. На странице **Цель защиты** выберите **To Azure** (В Azure) > **Без виртуализации или иное**.


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 7. Настройка исходной и целевой среды для репликации физического сервера в Azure](physical-walkthrough-source-target.md).

