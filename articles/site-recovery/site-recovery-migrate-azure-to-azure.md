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
ms.openlocfilehash: 02f68b68491250f89e8b0e3057f2363b177ab32e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Перенос виртуальных машин IaaS Azure между регионами Azure с помощью Azure Site Recovery
## <a name="overview"></a>Обзор
Вас приветствует служба Azure Site Recovery! Воспользуйтесь этой статьей для выполнения переноса виртуальных машин Azure из одного региона Azure в другой.
>[!NOTE]
>
> Сведения о репликация виртуальных машин Azure в другой регион для аварийного восстановления и миграции см. в [этом документе](site-recovery-azure-to-azure.md). Репликация Azure Site Recovery для виртуальных машин Azure сейчас доступна в предварительной версии.

Перед началом работы обратите внимание на следующее:

* В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: модель Azure Resource Manager и классическая модель. Портал Azure поддерживает обе модели развертывания. Основные шаги, необходимые для выполнения переноса, одинаковы при настройке Site Recovery как с помощью модели Resource Manager, так и с помощью классической модели. 

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Технические условия
Вот что нужно для этого развертывания:

* **Виртуальные машины IaaS**— виртуальные машины, которые требуется перенести. При переносе виртуальных машин используйте те же инструкции, что и для физических компьютеров.

## <a name="deployment-steps"></a>Шаги по развертыванию
В этом разделе описаны шаги развертывания на портале Azure.

1. [Создайте хранилище](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Включите репликацию](site-recovery-azure-to-azure.md) для виртуальных машин, которые требуется перенести, и выберите Azure в качестве источника. Сейчас собственная репликация виртуальных машин Azure с помощью управляемых дисков не поддерживается. Для переноса виртуальных машин с помощью управляемых дисков можно использовать параметр Physical to Azure (Из физической среды в Azure) в [этом документе](site-recovery-vmware-to-azure.md).
1. [Запустите отработку отказа](site-recovery-failover.md). После завершения начальной репликации вы можете запустить отработку отказа из одного региона Azure в другой. При необходимости вы можете создать план восстановления и запустить отработку отказа, чтобы перенести несколько виртуальных машин между регионами. [Узнайте подробнее](site-recovery-create-recovery-plans.md) о планах восстановления.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других сценариях репликации см. в статье [Что такое Site Recovery?](site-recovery-overview.md)
