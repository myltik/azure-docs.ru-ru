---
title: Резервное копирование большого числа виртуальных машин Azure в Azure | Документы Майкрософт
description: В этом руководстве рассматривается резервное копирование нескольких виртуальных машин Azure в хранилище служб восстановления.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: резервное копирование виртуальных машин; резервное копирование виртуальной машины; резервное копирование и аварийное восстановление
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 62cc623dc3130119c5ec803933012c5545d703e5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Резервное копирование большого числа виртуальных машин Azure в Azure

В этом руководстве рассматривается резервное копирование виртуальных машин Azure в хранилище служб восстановления. Большая часть процесса резервного копирования виртуальных машин приходится на подготовку. Прежде чем можно будет переходить к резервному копированию (или защите) виртуальной машины, вам потребуется выполнить [необходимые условия](backup-azure-arm-vms-prepare.md), чтобы подготовить среду для защиты виртуальных машин. 

> [!IMPORTANT]
> В рамках руководства предполагается, что группа ресурсов и виртуальная машина Azure уже созданы.

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

[Хранилище служб восстановления](backup-azure-recovery-services-vault-overview.md) представляет собой контейнер, который содержит точки восстановления для архивируемых элементов. Хранилище служб восстановления — это ресурс Azure, развертывание и управление которым осуществляется в составе группы ресурсов Azure. В рамках этого руководства хранилище служб восстановления создается в той же группе ресурсов, что и защищаемая виртуальная машина.


При первом использовании службы архивации Azure необходимо зарегистрировать поставщик службы восстановления Azure в вашей подписке. Если вы уже зарегистрировали поставщик в подписке, перейдите к следующему шагу.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Создайте хранилище службы восстановления с помощью командлета **New-AzureRmRecoveryServicesVault**. Не забудьте указать имя группы ресурсов и расположение, используемое при настройке виртуальной машины, архивацию которой вы выполняете. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Для многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входных данных. По этой причине объект хранилища служб восстановления резервных копий удобно хранить в переменной. Затем используйте командлет **Set-AzureRmRecoveryServicesBackupProperties**, чтобы задать для параметра **-BackupStorageRedundancy** значение [Geo-Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Резервное копирование виртуальных машин Azure

Перед запуском начальной архивации необходимо задать контекст хранилища. Контекст хранилища — это тип данных, защищаемых в хранилище. Создаваемое хранилище служб восстановления поставляется с политиками защиты и хранения по умолчанию. Политика защиты по умолчанию запускает задание резервного копирования каждый день в указанное время. Политика хранения по умолчанию хранит ежедневную точку восстановления в течение 30 дней. В рамках этого руководства примите политику по умолчанию. 

Используйте командлет **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**, чтобы задать контекст хранилища. Заданный контекст хранилища применяется ко всем последующим командлетам. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Используйте командлет **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** для запуска задания резервного копирования. Задание резервного копирования создает точку восстановления. Если это начальная архивация, точка восстановления будет полной резервной копией. Затем создаются добавочные резервные копии.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Удаление хранилища служб восстановления

Чтобы удалить хранилище служб восстановления, необходимо сначала удалить все точки восстановления в хранилище, а затем отменить регистрацию хранилища. Следующие команды подробно описывают эти шаги. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Устранение ошибок
Если во время резервного копирования виртуальной машины возникают проблемы, см. раздел [Устранение неполадок при архивации виртуальных машин Azure](backup-azure-vms-troubleshoot.md).

## <a name="next-steps"></a>Дополнительная информация
Настроив защиту виртуальных машин, ознакомьтесь со следующими статьями, в которых приводятся сведения о задачах управления и восстановлении виртуальных машин из точки восстановления.

* Сведения об изменении политики резервного копирования см. в описании [командлетов AzureRM.RecoveryServices.Backup для архивации виртуальных машин](backup-azure-vms-automation.md#create-a-protection-policy).
* [Мониторинг виртуальных машин и управление ими](backup-azure-manage-vms.md)
* [Восстановление виртуальных машин](backup-azure-arm-restore-vms.md)
