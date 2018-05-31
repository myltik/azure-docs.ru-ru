---
title: Удаление хранилища Site Recovery
description: Узнайте, как удалить хранилище Azure Site Recovery в зависимости от сценария Site Recovery.
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 1c069e8b09f71ac46017974dfd94945c404b16ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209898"
---
# <a name="delete-a-site-recovery-vault"></a>Удаление хранилища Site Recovery
Зависимости не позволяют удалить хранилище Azure Site Recovery. Действия, которые необходимо выполнить, различаются в зависимости от типа сценария Site Recovery: VMware в Azure, Hyper-V (с System Center Virtual Machine Manager и без него) в Azure и Azure Backup. Чтобы удалить хранилище, используемое в Azure Backup, см. сведения в статье [Удаление резервного хранилища в Azure](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Удаление хранилища Site Recovery 
Чтобы удалить хранилище, выполните рекомендуемые действия для своего сценария.

### <a name="vmware-vms-to-azure"></a>Виртуальные машины VMware в Azure

1. Удалите все защищенные виртуальные машины, выполнив действия, описанные в разделе [Отключение защиты для VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Удалите все политики репликации, выполнив действия, описанные в разделе [Удаление политики репликации](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Удалите ссылки на vCenter, выполнив действия, описанные в разделе [Удаление сервера vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Удалите сервер конфигурации, выполнив действия, описанные в разделе [Списание сервера конфигурации](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Удалите хранилище.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Виртуальные машины Hyper-V (с Virtual Machine Manager) в Azure
1. Удалите все защищенные виртуальные машины, выполнив действия, описанные в разделе [Отключение защиты для репликации виртуальной машины Hyper-V в Azure с помощью System Center VMM для сценария Azure](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Отмените связь и удалите все политики репликации, перейдя к хранилищу и выбрав **Инфраструктура Site Recovery** -> **For System Center VMM** (Для System Center VMM)  -> **Политики репликации**.

3.  Удалите ссылки на серверы Virtual Machine Manager, выполнив действия, описанные в разделе [Отмена регистрации подключенного сервера VMM](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Удалите хранилище.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Виртуальные машины Hyper-V (без Virtual Machine Manager) в Azure
1. Удалите все защищенные виртуальные машины, выполнив действия, описанные в разделе [Отключение защиты для виртуальной машины Hyper-V (Hyper-V для Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Отмените связь и удалите все политики репликации, перейдя к хранилищу и выбрав **Инфраструктура Site Recovery** -> **For Hyper-V Sites** (Для сайтов Hyper-V)  -> **Политики репликации**.

3. Удалите ссылки на серверы Hyper-V, выполнив действия, описанные в разделе [Отмена регистрации узла Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Удалите сайт Hyper-V.

5. Удалите хранилище.


## <a name="use-powershell-to-force-delete-the-vault"></a>Принудительное удаление хранилища с помощью PowerShell 

> [!Important]
> Если вы тестируете продукт и вас не беспокоит возможная потеря данных, используйте метод принудительного удаления для быстрого удаления хранилища и всех его зависимостей.
> Команда PowerShell удаляет все содержимое хранилища и является **необратимой**.

Используйте приведенные ниже команды, чтобы удалить хранилище Site Recovery, даже если оно содержит защищенные элементы.

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Ознакомьтесь с дополнительными сведениями о [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0) и [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
