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
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 86806c5dbafc1fd88c434dcee6292683d050cd2a
ms.contentlocale: ru-ru
ms.lasthandoff: 08/31/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Перенос виртуальных машин IaaS Azure между регионами Azure с помощью Azure Site Recovery
## <a name="overview"></a>Обзор
Вас приветствует служба Azure Site Recovery! Воспользуйтесь этой статьей для выполнения переноса виртуальных машин Azure из одного региона Azure в другой.
>[!NOTE]
>
> Сведения о репликация виртуальных машин Azure в другой регион для аварийного восстановления и миграции см. в [этом документе](site-recovery-azure-to-azure.md). Репликация Azure Site Recovery для виртуальных машин Azure сейчас доступна в предварительной версии.

Перед началом работы обратите внимание на следующее:

* В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: модель Azure Resource Manager и классическая модель. Кроме того, Azure предоставляет два портала — классический портал Azure, поддерживающий классическую модель развертывания, и портал Azure, поддерживающий обе модели развертывания. Основные шаги, необходимые для выполнения переноса, одинаковы при настройке Site Recovery как с помощью модели Resource Manager, так и с помощью классической модели. Приводимые в этой статье элементы пользовательского интерфейса и снимки экрана относятся к порталу Azure.



Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Предварительные требования
Вот что нужно для этого развертывания:

* **Виртуальные машины IaaS**— виртуальные машины, которые требуется перенести. При переносе виртуальных машин используйте те же инструкции, что и для физических компьютеров.

## <a name="deployment-steps"></a>Шаги по развертыванию
В этом разделе описаны шаги по развертыванию на новом портале Azure.

1. [Создайте хранилище](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Включите репликацию](site-recovery-azure-to-azure.md) для виртуальных машин, которые требуется перенести, и выберите Azure в качестве источника.
  >[!NOTE]
  >
  > Сейчас собственная репликация виртуальных машин Azure с помощью управляемых дисков не поддерживается. Для переноса виртуальных машин с помощью управляемых дисков можно использовать параметр Physical to Azure (Из физической среды в Azure) в [этом документе](site-recovery-vmware-to-azure.md).
3. [Запустите отработку отказа](site-recovery-failover.md). После завершения начальной репликации вы можете запустить отработку отказа из одного региона Azure в другой. При необходимости вы можете создать план восстановления и запустить отработку отказа, чтобы перенести несколько виртуальных машин между регионами. [Узнайте подробнее](site-recovery-create-recovery-plans.md) о планах восстановления.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других сценариях репликации см. в статье [Что такое Site Recovery?](site-recovery-overview.md)

