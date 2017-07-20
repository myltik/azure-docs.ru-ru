---
title: "Удаление хранилища служб восстановления"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Удаление хранилища служб восстановления
Зависимости не позволяют удалить хранилище служб восстановления, а действия, которые необходимо выполнить, отличаются в зависимости от типа сценария Azure Site Recovery. Возможны такие варианты: VMWare в Azure, Hyper-V (с VMM и без VMM) в Azure и Azure Backup. Чтобы удалить хранилище, используемое в Azure Backup, перейдите по [этой ссылке](../backup/backup-azure-delete-vault.md).

>[!Important]
>Если вы тестируете продукт и хотите быстро удалить хранилище, при этом вас не беспокоит возможная потеря данных, то можно использовать метод принудительного удаления хранилища и всех его зависимостей.

> Обратите внимание, что команда PowerShell удалит все содержимое хранилища. Этот шаг является необратимым.

## <a name="force-delete-vault-using-powershell"></a>Принудительное удаление хранилища с помощью Powershell

Выполните описанные ниже действия, чтобы удалить хранилище Site Recovery, даже если оно содержит защищенные элементы.

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



Выполните рекомендуемые действия (в указанном порядке) для своего сценария, чтобы удалить хранилище.

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>Удаление хранилища, используемого в Site Recovery для защиты виртуальных машин VMWare в Azure:
1. Убедитесь, что удалены все защищенные виртуальные машины. Узнайте, [как это сделать](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
2.  Убедитесь, что удалены все политики репликации. Узнайте, [как это сделать](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
3.  Убедитесь, что удалены ссылки на vCenter. Узнайте, [как это сделать](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).
4. Убедитесь, что удален сервер конфигурации. Узнайте, [как это сделать](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).
5. Теперь попробуйте удалить хранилище.


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Удаление хранилища, используемого в Site Recovery для защиты виртуальных машин Hyper-V (с VMM) в Azure:
1.  Убедитесь, что удалены все защищенные виртуальные машины. Узнайте, [как это сделать](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Убедитесь, что удалены все политики репликации. Узнайте, [как это сделать](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Удалите ссылки на серверы VMM. Узнайте, [как это сделать](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server).
-   Теперь попробуйте удалить хранилище.

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Удаление хранилища, используемого в Site Recovery для защиты виртуальных машин Hyper-V (без VMM) в Azure:
1. Убедитесь, что удалены все защищенные виртуальные машины. Узнайте, [как это сделать](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Убедитесь, что удалены все политики репликации. Узнайте, [как это сделать](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Удалите ссылки на серверы Hyper-V. Узнайте, [как это сделать](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).
-   Удалите сайт Hyper-V.
-   Теперь попробуйте удалить хранилище.

