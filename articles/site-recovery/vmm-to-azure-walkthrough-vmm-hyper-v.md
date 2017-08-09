---
title: "Подготовка System Center VMM для репликации Hyper-V в Azure | Документация Майкрософт"
description: "Здесь описывается, как подготовить сервер System Center VMM к репликации Hyper-V в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---

# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>Шаг 6. Подготовка серверов VMM и узлов Hyper-V к репликации Hyper-V в Azure

После настройки [компонентов Azure](vmm-to-azure-walkthrough-prepare-azure.md) для развертывания выполните инструкции в этой статье, чтоб подготовить локальные серверы VMM и узлы Hyper-V для взаимодействия с Azure Site Recovery.

Комментарии или вопросы технического характера можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-vmm-servers"></a>Подготовка серверов VMM

- Необходим по крайней мере один сервер VMM, соответствующий требованиям к поддержке для репликации Site Recovery (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Убедитесь, что сервер VMM подготовлен к [сетевому сопоставлению](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- Убедитесь, что сервер VMM может получить доступ к этим URL-адресам.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- При использовании правил брандмауэра на основе IP-адресов убедитесь, что эти правила разрешают обмен данными с Azure.
- Разрешите доступ для [диапазонов IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) и использование порта HTTPS (443).
- Необходимо разрешить доступ для диапазонов IP-адресов региона Azure, в котором располагается ваша подписка, и региона "Западная часть США" (используется для контроля доступа и управления удостоверениями).

Во время развертывания Site Recovery скачайте поставщик Site Recovery и установите его на каждом сервере VMM. Сервер VMM регистрируется в хранилище служб восстановления.




## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 7. Настройка хранилища для репликации Hyper-V](vmm-to-azure-walkthrough-create-vault.md).


