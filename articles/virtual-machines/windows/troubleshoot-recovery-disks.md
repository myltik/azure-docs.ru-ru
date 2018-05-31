---
title: Использование виртуальной машины Windows для устранения неполадок с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как устранять неполадки с виртуальными машинами Windows в Azure, подключив диск операционной системы к виртуальной машине восстановления с помощью Azure PowerShell.
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 408429d0f8697b8b807e386dbcf2eade29938249
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271697"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью Azure PowerShell
Если возникает проблема с загрузкой или диском на виртуальной машине Windows в Azure, возможно, вам нужно устранить неполадки, связанные с самим виртуальным жестким диском. Например, такая ситуация может возникнуть из-за сбоя обновления приложения, который мешает успешно загрузить виртуальную машину. В этой статье подробно описано, как с помощью Azure PowerShell подключить виртуальный жесткий диск к другой виртуальной машине Windows для устранения ошибок, а затем восстановить исходную виртуальную машину.


## <a name="recovery-process-overview"></a>Обзор процесса восстановления
Процесс устранения неполадок выглядит следующим образом.

1. Удалите виртуальную машину, на которой возникли проблемы, сохранив ее виртуальные жесткие диски.
2. Присоедините и подключите виртуальный жесткий диск к другой виртуальной машине Windows для устранения неполадок.
3. Подключитесь к этой виртуальной машине. Измените файлы или запустите средства, которые нужны для устранения неполадок на исходном виртуальном жестком диске.
4. Отключите и отсоедините виртуальный жесткий диск от виртуальной машины, на которой выполняется устранение неполадок.
5. Создайте другую виртуальную машину, используя исходный виртуальный жесткий диск.

Сведения о виртуальной машине, используемой управляемый диск, см. в разделе [Устранение неполадок виртуальной машины с управляемым диском путем подключения нового диска операционной системы](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Убедитесь, что у вас установлена и настроена [последняя версия Azure PowerShell](/powershell/azure/overview) и выполнен вход в подписку.

```powershell
Connect-AzureRmAccount
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `mystorageaccount` и `myVM`.


## <a name="determine-boot-issues"></a>Выявление проблем при загрузке
Вы можете просмотреть снимок экрана виртуальной машины в Azure, чтобы устранить неполадки загрузки. Этот снимок экрана может помочь определить, почему виртуальная машина не загружается. Приведенный ниже пример получает снимок экрана виртуальной машины Windows `myVM`, расположенной в группе ресурсов `myResourceGroup`.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Просмотрите его, чтобы определить причину сбоя загрузки виртуальной машины. Обратите внимание на соответствующие сообщения об ошибках или коды ошибок.


## <a name="view-existing-virtual-hard-disk-details"></a>Просмотр сведений для существующего виртуального жесткого диска
Прежде чем присоединить виртуальный жесткий диск к другой виртуальной машине, следует определить имя этого виртуального жесткого диска.

В следующем примере возвращаются сведения о виртуальной машине `myVM` в группе ресурсов `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Найдите `Vhd URI` в разделе `StorageProfile` выходных данных предыдущей команды. Приведенный ниже усеченный пример выходных данных содержит `Vhd URI` в конце блока кода.

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Удаление существующей виртуальной машины
Виртуальные жесткие диски и виртуальные машины — это разные ресурсы в Azure. Виртуальный жесткий диск является местом хранения операционной системы, приложений и конфигурации. Виртуальная машина — это просто метаданные, которые определяют размер или расположение объекта, а также ссылки на ресурсы, такие как виртуальные жесткие диски или виртуальные сетевые адаптеры. При присоединении виртуального жесткого диска к виртуальной машине для него регистрируется аренда. Диски данных можно присоединять и отсоединять во время работы виртуальной машины, но диск операционной системы отсоединить невозможно, пока ресурс виртуальной машины не будет удален. Аренда сохраняет привязку диска операционной системы к виртуальной машине, даже когда эта виртуальная машина остановлена или отменено ее распределение.

Поэтому для восстановления виртуальной машины нужно прежде всего удалить ресурс виртуальной машины. Удаление виртуальной машины не повлияет на виртуальные жесткие диски, размещенные в учетной записи хранения. После удаления виртуальной машины присоедините виртуальный жесткий диск к другой виртуальной машине, чтобы устранить неполадки.

В следующем примере виртуальная машина `myVM` удаляется из группы ресурсов `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Дождитесь, пока удаление завершится, прежде чем присоединять виртуальный жесткий диск к другой виртуальной машине. Чтобы присоединить виртуальный жесткий диск к другой виртуальной машине, необходимо снять аренду, которая привязывает диск к виртуальной машине.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Присоединение существующего виртуального жесткого диска к другой виртуальной машине
В следующих нескольких шагах описывается использование другой виртуальной машины для устранения неполадок. Присоедините существующий виртуальный жесткий диск к виртуальной машине для устранения неполадок, чтобы просматривать и изменять содержимое диска. Этот процесс позволяет, например, исправить ошибки конфигурации или изучить дополнительные журналы протоколов или системы. Выберите или создайте другую виртуальную машину, которая будет использоваться для устранения неполадок.

При присоединении существующего виртуального жесткого диска укажите URL-адрес диска, полученный в предыдущей команде `Get-AzureRmVM`. В следующем примере существующий виртуальный жесткий диск присоединяется к виртуальной машине для устранения неполадок `myVMRecovery` в группе ресурсов `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Для добавления диска необходимо указать его размер. Так как мы подключаем существующий диск, `-DiskSizeInGB` имеет значение `$null`. Это значение обеспечивает правильное подключение диска данных без необходимости определять его фактический размер.


## <a name="mount-the-attached-data-disk"></a>Подключение присоединенного диска данных

1. Подключитесь к виртуальной машине для устранения неполадок по протоколу RDP, используя соответствующие учетные данные. Следующий пример скачивает RDP-файл подключения для виртуальной машины `myVMRecovery` в группе ресурсов `myResourceGroup` и сохраняет его в паку `C:\Users\ops\Documents`.

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Диск данных будет автоматически обнаружен и подключен. Просмотрите список подключенных томов, чтобы определить букву диска, как показано ниже.

    ```powershell
    Get-Disk
    ```

    В следующем примере выходных данных показан виртуальный жесткий диск, подключенный как диск **2**. (Для просмотра буква диска можно также использовать `Get-Volume`).

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Устранение неполадок на исходном виртуальном жестком диске
Теперь, когда существующий виртуальный жесткий диск подключен, вы можете выполнить любые необходимые действия по обслуживанию и (или) устранению неполадок. После устранения проблем выполните следующие действия.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Отключение и отсоединение исходного виртуального жесткого диска
После устранения ошибок отключите и отсоедините существующий виртуальный жесткий диск от виртуальной машины, использованный для устранения неполадок. Виртуальный жесткий диск нельзя использовать с другой виртуальной машиной, пока вы не отмените аренду, присоединяющую виртуальный жесткий диск к виртуальной машине для устранения неполадок.

1. Из сеанса RDP отключите диск данных на виртуальной машине восстановления. Для этого нужен номер диска из предыдущего командлета `Get-Disk`. Используйте `Set-Disk`, чтобы отключить диск.

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Убедитесь, что диск отключен, еще раз выполнив `Get-Disk`. В следующем примере выходных данных показано, что теперь диск отключен.

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Выйдите из сеанса RDP. Из сеанса Azure PowerShell удалите виртуальный жесткий диск с виртуальной машины для устранения неполадок.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Создание виртуальной машины из исходного жесткого диска
Чтобы создать виртуальную машину из исходного виртуального жесткого диска, используйте [этот шаблон Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Фактический шаблон JSON доступен по следующей ссылке:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

Этот шаблон развертывает виртуальную машину в существующую виртуальную сеть, используя URL-адрес виртуального жесткого диска из использованной выше команды. В следующем примере шаблон развертывается в группе ресурсов `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Укажите сведения в ответ на запросы шаблона, такие как имя виртуальной машины, тип операционной системы и размер виртуальной машины. Используется то же значение `osDiskVhdUri`, что и при присоединении существующего виртуального жесткого диска к виртуальной машине для устранения неполадок.


## <a name="re-enable-boot-diagnostics"></a>Восстановление диагностики загрузки

При создании виртуальной машины на основе существующего виртуального жесткого диска диагностика загрузки не всегда автоматически включена. В следующем примере на виртуальной машине `myVMDeployed` в группе ресурсов `myResourceGroup` включается расширение диагностики:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Устранение неполадок виртуальной машины с управляемым диском путем подключения нового диска операционной системы
1. Остановите затронутую виртуальную машину Windows с управляемым диском.
2. [Создайте моментальный снимок управляемого диска](snapshot-copy-managed-disk.md) операционной системы виртуальной машины.
3. [Создайте управляемый диск на основе моментального снимка](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Подключите управляемый диск в качестве диска данных виртуальной машины](attach-disk-ps.md).
5. [Замените диск данных из шага 4 на диск операционной системы](os-disk-swap.md).

## <a name="next-steps"></a>Дополнительная информация
При возникновении проблем с подключением к виртуальной машине ознакомьтесь со статьей [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Для устранения проблем с доступом к приложениям, выполняющимся на виртуальной машине, прочитайте статью [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Дополнительные сведения об использовании Resource Manager вы найдете в статье [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
