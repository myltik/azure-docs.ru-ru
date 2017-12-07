---
title: "Миграция виртуальных машин IaaS в Azure между регионами Azure | Документация Майкрософт"
description: "Используйте службу Site Recovery для переноса виртуальных машин IaaS Azure из одного региона Azure в другой."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Перенос виртуальных машин IaaS Azure между регионами Azure с помощью Azure Site Recovery

Вас приветствует служба Azure Site Recovery! Воспользуйтесь этой статьей для выполнения переноса виртуальных машин Azure из одного региона Azure в другой.
>[!NOTE]
>
> Сведения о репликация виртуальных машин Azure в другой регион для аварийного восстановления и миграции см. в [этом документе](../site-recovery-azure-to-azure.md). Репликация Azure Site Recovery для виртуальных машин Azure сейчас доступна в предварительной версии.


## <a name="prerequisites"></a>Предварительные требования
Вот что нужно для этого развертывания:

* **Виртуальные машины IaaS**— виртуальные машины, которые требуется перенести. При переносе виртуальных машин используйте те же инструкции, что и для физических компьютеров.

## <a name="deployment-steps"></a>Шаги по развертыванию
В этом разделе описаны шаги по развертыванию на новом портале Azure.

1. [Создайте хранилище](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Включите репликацию]../(site-recovery-azure-to-azure.md) для виртуальных машин, которые нужно перенести, и выберите Azure в качестве источника.
  >[!NOTE]
  >
  > Сейчас собственная репликация виртуальных машин Azure с помощью управляемых дисков не поддерживается. Для переноса виртуальных машин с помощью управляемых дисков можно использовать параметр Physical to Azure (Из физической среды в Azure) в [этом документе](../site-recovery-vmware-to-azure.md).
3. [Запустите отработку отказа](../site-recovery-failover.md). После завершения начальной репликации вы можете запустить отработку отказа из одного региона Azure в другой. При необходимости вы можете создать план восстановления и запустить отработку отказа, чтобы перенести несколько виртуальных машин между регионами. [Узнайте подробнее](../site-recovery-create-recovery-plans.md) о планах восстановления.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других сценариях репликации см. в статье [Что такое Site Recovery?](../site-recovery-overview.md)
