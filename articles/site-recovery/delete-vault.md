---
title: "Удаление хранилища Site Recovery"
description: "Узнайте, как удалить хранилище Azure Site Recovery в зависимости от сценария Site Recovery."
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
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: b95b9defa0a037f7d7d3ef36b99bc7c53c751050
ms.contentlocale: ru-ru
ms.lasthandoff: 07/21/2017

---
# <a name="delete-a-site-recovery-vault"></a>Удаление хранилища Site Recovery
Зависимости не позволяют удалить хранилище Azure Site Recovery. Действия, которые необходимо выполнить, различаются в зависимости от типа сценария Site Recovery: VMware в Azure, Hyper-V (с System Center Virtual Machine Manager и без него) в Azure и Azure Backup. Чтобы удалить хранилище, используемое в Azure Backup, см. сведения в статье [Удаление резервного хранилища в Azure](../backup/backup-azure-delete-vault.md).

>[!Important]
>Если вы тестируете продукт и вас не беспокоит возможная потеря данных, используйте метод принудительного удаления для быстрого удаления хранилища и всех его зависимостей.

> Команда PowerShell удаляет все содержимое хранилища и является необратимой.

## <a name="use-powershell-to-force-delete-the-vault"></a>Принудительное удаление хранилища с помощью PowerShell 

Используйте приведенные ниже команды, чтобы удалить хранилище Site Recovery, даже если оно содержит защищенные элементы.

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault


## <a name="delete-a-site-recovery-vault"></a>Удаление хранилища Site Recovery 
Чтобы удалить хранилище, выполните рекомендуемые действия для своего сценария.

### <a name="vmware-vms-to-azure"></a>Виртуальные машины VMware в Azure

1. Удалите все защищенные виртуальные машины, выполнив действия, описанные в разделе [Отключение защиты для VMware](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Удалите все политики репликации, выполнив действия, описанные в разделе [Удаление политики репликации](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Удалите ссылки на vCenter, выполнив действия, описанные в разделе [Удаление vCenter](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).

4. Удалите сервер конфигурации, выполнив действия, описанные в разделе [Списание сервера конфигурации](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).

5. Удалите хранилище.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Виртуальные машины Hyper-V (с Virtual Machine Manager) в Azure
1. Удалите все защищенные виртуальные машины, выполнив действия, описанные в разделе [Отключение защиты для виртуальной машины VMware или физического сервера](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Удалите все политики репликации, выполнив действия, описанные в разделе [Удаление политики репликации](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3.  Удалите ссылки на серверы Virtual Machine Manager, выполнив действия, описанные в разделе [Отмена регистрации подключенного сервера VMM](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server).

4.  Удалите хранилище.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Виртуальные машины Hyper-V (без Virtual Machine Manager) в Azure
1. Удалите все защищенные виртуальные машины, выполнив действия, описанные в разделе [Отключение защиты для виртуальной машины VMware или физического сервера](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Удалите все политики репликации, выполнив действия, описанные в разделе [Удаление политики репликации](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Удалите ссылки на серверы Hyper-V, выполнив действия, описанные в разделе [Отмена регистрации узла Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Удалите сайт Hyper-V.

5. Удалите хранилище.

