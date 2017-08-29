---
title: "Необходимые условия для репликации в Azure с использованием Azure Site Recovery | Документация Майкрософт"
description: "В этой статье перечислены необходимые условия для репликации виртуальных машин и физических компьютеров в Azure с использованием службы Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Предварительные требования для репликации виртуальных машин Azure в другой регион с помощью Azure Site Recovery

> [!div class="op_single_selector"]
> * [Репликация из Azure в Azure](site-recovery-azure-to-azure-prereq.md)
> * [Репликация из локальной среды в Azure ](site-recovery-prereq.md)

Служба Azure Site Recovery помогает реализовать стратегию, непрерывности бизнес-процессов и аварийного восстановления (BCDR) за счет координации следующих процессов:
* Репликация виртуальных машин Azure в другой регион Azure.
* Репликация локальных физических серверов и виртуальных машин в Azure или в дополнительный центр обработки данных. 

При возникновении сбоев в исходном расположении можно выполнить отработку отказа в дополнительное расположение, чтобы сохранить доступность приложений и рабочих нагрузок. При восстановлении нормального режима работы основного расположения можно восстановить размещение в него. Дополнительные сведения о службе Site Recovery см. в [этой статье](site-recovery-overview.md).

В этой статье перечислены предварительные требования для начала репликации из локальной среды в Azure с помощью Site Recovery.

Комментарии или вопросы технического характера можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Требования Azure

**Требование** | **Дополнительные сведения**
--- | ---
**Учетная запись Azure** | Учетная запись [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
**Служба Azure Site Recovery** | Дополнительные сведения о ценах на службу Site Recovery см. [здесь](https://azure.microsoft.com/pricing/details/site-recovery/). Мы рекомендуем создать хранилище служб восстановления в целевом регионе Azure, который нужно использовать в качестве расположения для аварийного восстановления. Например, если исходные виртуальные машины работают в регионе "Восточная часть США", а вы хотите выполнить репликацию в регион "Центральная часть США", мы рекомендуем создать хранилище в этом регионе.|
**Производительность Azure** | Для целевого региона Azure, который нужно использовать в качестве расположения для аварийного восстановления, необходимо иметь подписку с достаточной пропускной способностью для виртуальных машин, учетных записей хранения и сетевых компонентов. Если вам нужно добавить емкость, обратитесь в службу поддержки.
**Рекомендации по выбору хранилища** | Во избежание проблем с производительностью следуйте [рекомендациям по выбору хранилища](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) для исходных виртуальных машин Azure. Если использовать параметры по умолчанию, Site Recovery создает необходимые учетные записи хранения на основе исходной конфигурации. Если вы настраиваете и выбираете собственные параметры, учитывайте [целевые показатели масштабирования для дисков виртуальных машин](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Рекомендации по организации сети** | Исходящие подключения виртуальной машины Azure для конкретных URL-адресов или диапазонов IP-адресов нужно добавить в список разрешений. Дополнительные сведения см. в статье [Руководство по организации сети для репликации виртуальных машин Azure](site-recovery-azure-to-azure-networking-guidance.md).
**Azure** | Убедитесь, что на виртуальной машине Windows или Linux есть все новые корневые сертификаты. Если новых корневых сертификатов нет, виртуальную машину нельзя регистрировать в Site Recovery из-за ограничений безопасности.

>[!NOTE]
>Дополнительные сведения о поддержке определенных конфигураций см. в [таблице поддержки](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Дальнейшие действия
- Ознакомьтесь со статьей [Руководство по организации сети для репликации виртуальных машин Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Включите защиту рабочих нагрузок, [выполнив репликацию виртуальных машин Azure](site-recovery-azure-to-azure.md).

