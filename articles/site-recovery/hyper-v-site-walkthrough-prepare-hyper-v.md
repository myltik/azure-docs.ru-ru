---
title: "Подготовка узлов Hyper-V (без System Center VMM) для репликации в Azure | Документация Майкрософт"
description: "В этой статье описывается подготовка узлов Hyper-V для репликации в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>Шаг 6. Подготовка узлов Hyper-V для репликации в Azure

Используйте инструкции в этой статье, чтобы подготовить локальные узлы Hyper-V к взаимодействию со службой Azure Site Recovery.

Комментарии или вопросы технического характера можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Подготовка узлов

- Убедитесь, что узлы Hyper-V соответствуют [предварительным требованиям](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Убедитесь, что узлы имеют доступ к нужным URL-адресам.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- При использовании правил брандмауэра на основе IP-адресов убедитесь, что эти правила разрешают обмен данными с Azure.
- Разрешите доступ для [диапазонов IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) и использование порта HTTPS (443).
- Необходимо разрешить доступ для диапазонов IP-адресов региона Azure, в котором располагается ваша подписка, и региона "Западная часть США" (используется для контроля доступа и управления удостоверениями).

При развертывании Site Recovery вы добавляете узлы Hyper-V, содержащие виртуальные машины, которые необходимо реплицировать на сайт Hyper-V. Поставщик Site Recovery и агент служб восстановления устанавливаются на каждом узле. Сайт Hyper-V регистрируется в хранилище служб восстановления.

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 7. Настройка хранилища для репликации Hyper-V](hyper-v-site-walkthrough-create-vault.md).

