---
title: Краткое руководство по Azure. Резервное копирование виртуальной машины с помощью PowerShell | Документация Майкрософт
description: Узнайте, как создавать резервные копии виртуальных машин с помощью Azure PowerShell
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: ''
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 91303368f96b477f0756e4d3b9d19601797f0ba8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Резервное копирование виртуальной машины в Azure с помощью PowerShell
Модуль Azure PowerShell используется для создания и администрирования ресурсов Azure с помощью командной строки или скриптов. Для защиты данных можно создавать архивы с регулярным интервалом. Служба Azure Backup создает точки восстановления, которые могут храниться в геоизбыточных хранилищах служб восстановления. В этой статье объясняется, как создать резервную копию виртуальной машины с помощью модуля Azure PowerShell. Эти действия можно также выполнить с помощью [Azure CLI](quick-backup-vm-cli.md) или [портала Azure](quick-backup-vm-portal.md).

В этом руководстве объясняется, как включить резервное копирование существующей виртуальной машины Azure. Если вам необходимо создать виртуальную машину, см. руководство по [созданию виртуальной машины с помощью Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Для работы с этим кратким руководством требуется модуль Azure PowerShell 4.4 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="log-in-to-azure"></a>Вход в Azure
Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

При первом использовании службы Azure Backup зарегистрируйте поставщик служб восстановления Azure в своей подписке с помощью команды [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Создание хранилищ служб восстановления
Хранилище служб восстановления — это логический контейнер, в котором хранятся данные резервного копирования для каждого защищенного ресурса, например виртуальных машин Azure. Когда выполняется задание резервного копирования для защищенного ресурса, в хранилище служб восстановления создается точка восстановления. Позже вы сможете использовать одну из этих точек восстановления, чтобы восстановить данные до определенной точки во времени.

Создайте хранилище службы восстановления с помощью командлета [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Укажите те же группу ресурсов и расположение, что и для виртуальной машины, которую необходимо защитить. Если вы создали виртуальную машину с помощью этого [примера скрипта](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json), группа будет ресурсов называться *myResourceGroup*, виртуальная машина — *myVM*, а ресурсы будут находиться в расположении *Западная Европа*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

По умолчанию в качестве хранилища задано геоизбыточное хранилище. Для дополнительной защиты данных этот уровень избыточности хранилища гарантирует, что данные резервного копирования реплицируются в дополнительный регион Azure, который находится в сотнях километров от основного.

Чтобы использовать это хранилище для выполнения следующих инструкций, определите контекст хранилища с помощью командлета [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext).

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Включение резервного копирования для виртуальной машины Azure
Создавая и используя политики, вы можете определить, когда выполнять задание резервного копирования и как долго хранить точку восстановления. Согласно политике защиты по умолчанию задание резервного копирования выполняется каждый день, а точки восстановления хранятся в течение 30 дней. Вы можете использовать значения политики по умолчанию, чтобы быстро защитить виртуальную машину. Сначала задайте политику по умолчанию с помощью командлета [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Чтобы включить защиту резервной копии, используйте командлет [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Укажите необходимую политику, а затем — группу ресурсов и виртуальную машину, которые нужно защитить:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Запуск задания резервного копирования
Чтобы начать резервное копирование незамедлительно, не ожидая, пока политика по умолчанию запустит задание в запланированное время, используйте командлет [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). В ходе первого задания резервного копирования создается точка полного восстановления. Во всех заданиях после начального резервного копирования создаются добавочные точки восстановления. Добавочные точки восстановления требуют мало места и времени, так как они позволяют передать только изменения, внесенные с момента последнего резервного копирования.

Укажите контейнер в хранилище служб восстановления, в котором хранятся данные резервной копии с помощью командлета [Get AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Каждая виртуальная машина для резервного копирования обрабатывается как элемент. Чтобы запустить задание резервного копирования, получите сведения об элементе своей виртуальной машины с помощью командлета [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Так как в ходе первого задания резервного копирования создается точка полного восстановления, процесс может занять до 20 минут.


## <a name="monitor-the-backup-job"></a>Мониторинг задания резервного копирования
Чтобы отслеживать состояние задания резервного копирования, используйте командлет [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

В результате вы получите приблизительно следующие выходные данные. Они указывают на то, что задание резервного копирования выполняется (**InProgress**):

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Если в столбце *Status* (Состояние) задания резервного копирования отображается значение *Completed* (Выполнено), это означает, что виртуальная машина защищена с помощью служб восстановления и для нее сохранена точка полного восстановления.


## <a name="clean-up-deployment"></a>Очистка развертывания
Если защита больше не нужна, вы можете отключить ее на виртуальной машине, удалить точки восстановления и хранилище служб восстановления, а затем удалить группу ресурсов и связанные ресурсы виртуальной машины. Если вы использовали существующую виртуальную машину, можете пропустить последний командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), чтобы оставить группу ресурсов и виртуальную машину.

Если вы планируете ознакомиться с руководством по службе Backup, в котором объясняется, как восстанавливать данные виртуальной машины, пропустите шаги в этом разделе и перейдите к [дальнейшим действиям](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве вы создали хранилище служб восстановления, включили защиту на виртуальной машине и создали начальную точку восстановления. Дополнительные сведения о службе Azure Backup и службах восстановления см. в соответствующих руководствах.

> [!div class="nextstepaction"]
> [Резервное копирование нескольких виртуальных машин Azure](./tutorial-backup-vm-at-scale.md)
