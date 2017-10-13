---
title: "Создание хранилища для репликации Hyper-V на дополнительный сайт с использованием Azure Site Recovery | Документация Майкрософт"
description: "Здесь описывается, как создать хранилище при репликации виртуальных машин Hyper-V на дополнительный сайт System Center VMM с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>Шаг 5. Создание хранилища для репликации Hyper-V на дополнительный сайт

После подготовки локальных [серверов System Center Virtual Machine Manager (VMM), а также узлов и кластеров Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md) к репликации Hyper-V на дополнительный сайт с помощью [Azure Site Recovery](site-recovery-overview.md) можно создать хранилище служб восстановления и выбрать сценарий репликации.

Любые комментарии, которые возникнут после прочтения этой статьи, можно добавить в конце этой страницы или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Выбор цели защиты

Выберите целевые объекты и место для репликации.

1. Щелкните **Site Recovery** > **Шаг 1. Подготовка инфраструктуры** > **Цель защиты**.
2. Выберите **Для сайта восстановления** и **Yes, with Hyper-V** (Да, с использованием Hyper-V).
3. Выберите **Да**, чтобы указать управление узлами Hyper-V с помощью VMM.
4. Выберите **Да**, если используется дополнительный сервер VMM. Если вы развертываете репликацию между облаками, развернутыми на одном сервере VMM, щелкните **Нет**. Нажмите кнопку **ОК**.

    ![Выбор цели](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 6. Настройка исходных и целевых параметров репликации](vmm-to-vmm-walkthrough-source-target.md).