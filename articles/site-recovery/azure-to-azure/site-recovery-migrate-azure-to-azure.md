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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Перенос виртуальных машин IaaS Azure между регионами Azure с помощью Azure Site Recovery

В этой статье описано, как выполнять миграцию виртуальных машин Azure между регионами Azure с помощью службы [Site Recovery](../site-recovery-overview.md).

## <a name="prerequisites"></a>Предварительные требования

Вам нужны виртуальные машины IaaS, которые требуется перенести.

## <a name="deployment-steps"></a>Шаги по развертыванию

1. [Создайте хранилище](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Включите репликацию](azure-to-azure-tutorial-enable-replication.md#enable-replication) для виртуальных машин, которые требуется перенести, и выберите Azure в качестве источника. Сейчас собственная репликация виртуальных машин Azure с помощью управляемых дисков не поддерживается.
3. [Запустите отработку отказа](../site-recovery-failover.md). После завершения начальной репликации вы можете запустить отработку отказа из одного региона Azure в другой. При необходимости вы можете создать план восстановления и запустить отработку отказа, чтобы перенести несколько виртуальных машин между регионами. [Узнайте подробнее](../site-recovery-create-recovery-plans.md) о планах восстановления.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других сценариях репликации см. в статье [Сведения о службе Site Recovery](../site-recovery-overview.md).
