---
title: "Установка службы Mobility Service для репликации из VMware в Azure | Документация Майкрософт"
description: "В этой статье описано, как с помощью службы Azure Site Recovery установить агент службы Mobility Service для репликации из VMware в Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017

---

# <a name="step-10-install-the-mobility-service"></a>Шаг 10. Установка службы Mobility Service


В этой статье описано, как настроить параметры исходной и целевой среды при репликации локальных виртуальных машин VMware в Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки. Ее необходимо установить на каждом компьютере, который будет реплицироваться в Azure.

Службу Mobility Service можно установить вручную, используя принудительную установку с сервера обработки Site Recovery, когда репликация включена, или с помощью инструмента System Center Configuration Manager. При использовании принудительной установки служба устанавливается на виртуальную машину после включения репликации.

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Установка вручную

1. Проверьте, выполнены ли [предварительные требования](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) для установки вручную.
2. Следуйте [этим инструкциям](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui), чтобы выполнить установку вручную с помощью портала.
3. Если вам удобнее выполнить установку из командной строки, то следуйте [этим инструкциям](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Установка с сервера обработки

Если требуется принудительно установить службу Mobility Service с сервера обработки при включении репликации виртуальной машины, то необходима учетная запись, которая может использоваться сервером обработки для доступа к виртуальной машине. Эта учетная запись предназначена только для принудительной установки.

1. Необходимо [создать учетную запись](vmware-walkthrough-prepare-vmware.md), которая может использоваться для принудительной установки. Затем укажите учетную запись, которую необходимо использовать при настройке параметров исходной среды во время развертывания Site Recovery.
2. Затем следуйте [этим инструкциям](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery), чтобы принудительно установить службу Mobility Service на виртуальные машины под управлением Windows или Linux.

## <a name="other-methods"></a>Другие методы

Кроме того, вы можете узнать больше об [установке службы Mobility Service с помощью Configuration Manager](site-recovery-install-mobility-service-using-sccm.md) или с помощью [Azure Automation DSC](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к разделу [Шаг 11. Включение репликации](vmware-walkthrough-enable-replication.md).

