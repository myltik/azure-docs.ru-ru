---
title: "Установка службы Mobility Service для репликации из физических серверов в Azure | Документация Майкрософт"
description: "В этой статье описано, как с помощью службы Azure Site Recovery установить на физических серверах агент службы Mobility Service для репликации в Azure."
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
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017

---

# <a name="step-9-install-the-mobility-service"></a>Шаг 9. Установка службы Mobility Service


В этой статье описано, как установить компонент службы Mobility Service при репликации локальных физических серверов Windows или Linux в Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки. Ее необходимо установить на каждом сервере, который будет реплицироваться в Azure.

Службу Mobility Service можно установить вручную, используя принудительную установку с сервера обработки Site Recovery, когда репликация включена, или с помощью такого средства, как System Center Configuration Manager. При использовании принудительной установки служба устанавливается на сервере после включения репликации.

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Установка вручную

1. Проверьте, выполнены ли [предварительные требования](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) для установки вручную.
2. Следуйте [этим инструкциям](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui), чтобы выполнить установку вручную с помощью портала.
3. Если вам удобнее выполнить установку из командной строки, то следуйте [этим инструкциям](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Установка с сервера обработки

Если требуется принудительно установить службу Mobility Service с сервера обработки при включении репликации компьютера, то необходима учетная запись, которая может использоваться сервером обработки для доступа к компьютеру. Эта учетная запись предназначена только для принудительной установки.

1. Если вы еще не создали учетную запись, придерживайтесь следующих рекомендаций:

    - Можно использовать доменную или локальную учетную запись.
    - Для Windows, если учетная запись домена не используется, на локальном компьютере потребуется отключить контроль удаленного доступа пользователей. Для этого в разделе реестра **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** добавьте запись **LocalAccountTokenFilterPolicy** DWORD и задайте для нее значение 1.
    - Если требуется добавить запись в реестр Windows из командной строки, введите:

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - Для Linux учетная запись должна принадлежать привилегированному пользователю на исходном сервере Linux.

2. Затем следуйте [этим инструкциям](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery), чтобы принудительно установить службу Mobility Service на виртуальные машины под управлением Windows или Linux.

## <a name="other-installation-methods"></a>Другие методы установки

- Сведения об установке службы Mobility Service с помощью Configuration Manager см. [здесь](site-recovery-install-mobility-service-using-sccm.md).
- Сведения об установке службы Mobility Service с помощью Azure Automation DSC см. [здесь](site-recovery-automate-mobility-service-install.md).


## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 10. Включение репликации для физических серверов в Azure](physical-walkthrough-enable-replication.md).

