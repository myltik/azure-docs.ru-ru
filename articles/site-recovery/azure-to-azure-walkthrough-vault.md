---
title: "Настройка хранилища для репликации виртуальной машины Azure между регионами с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье кратко описаны шаги по настройке хранилища для репликации Azure между регионами Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.contentlocale: ru-ru
ms.lasthandoff: 08/02/2017

---

# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>Шаг 4. Настройка хранилища для репликации из Azure в Azure

После [планирования сетей](azure-to-azure-walkthrough-network.md) вы можете воспользоваться этой статьей, чтобы настроить хранилище для виртуальных машин Azure, реплицируемых в другой регион Azure, с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

- После выполнения инструкций в этой статье у вас будет настроенное хранилище служб восстановления.
- Все комментарии можно добавить в конце этой статьи. Вопросы можно задать на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



>[!NOTE]
>
> Репликация виртуальных машин Azure сейчас доступна в режиме предварительной версии.




## <a name="create-a-vault"></a>Создание хранилища

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Советуем создавать хранилище служб восстановления в расположении, куда вы хотите реплицировать ваши виртуальные машины. Например, если целевое расположение центральная часть США, создайте хранилище в **центральной части США**.


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 5. Включение репликации для виртуальных машин Azure](azure-to-azure-walkthrough-enable-replication.md).

