---
title: "Создание управляемого образа в Azure | Документация Майкрософт"
description: "Создание управляемого образа универсальной виртуальной машины или виртуального жесткого диска в Azure. Образы можно использовать для создания нескольких виртуальных машин, использующих управляемые диски."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: e428b755f6696bd6d4047ad77579a8e9665dfbd8
ms.contentlocale: ru-ru
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Создание управляемого образа универсальной виртуальной машины в Azure

Ресурс управляемого образа можно создать из универсальной виртуальной машины, которая хранится в виде управляемого диска или неуправляемых дисков в учетной записи хранения. Затем образ можно использовать для создания нескольких виртуальных машин. 


## <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Нажмите кнопку **ОК**.
   
    ![Запуск Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. Не перезапускайте виртуальную машину.


## <a name="create-a-managed-image-in-the-portal"></a>Создание управляемого образа на портале 

1. Откройте [портал](https://portal.azure.com).
2. Нажмите знак "плюс" (+), чтобы создать ресурс.
3. В поле фильтрации поиска введите **Image**.
4. Выберите **Image** (Образ) из результатов.
5. В колонке **Image** (Образ) нажмите кнопку **Создать**.
6. В поле **Имя** введите имя образа.
7. При наличии нескольких подписок выберите подписку, которую вы хотите использовать, из раскрывающегося списка **Подписка**.
7. В поле **Группа ресурсов** выберите **Создать** и введите имя или выберите **From existing** (Из существующего) и выберите из раскрывающегося списка используемую группу ресурсов.
8. В поле **Расположение** выберите расположение группы ресурсов.
9. В поле **Тип ОС** выберите тип операционной системы: Windows или Linux.
11. В разделе **Большой двоичный объект хранилища** нажмите кнопку **Обзор**, чтобы найти виртуальный жесткий диск в службе хранилища Azure.
12. В поле **Тип учетной записи** выберите Standard_LRS или Premium_LRS. Для уровня "Стандартный" используются жесткие диски, а для уровня "Премиум" — твердотельные накопители. Для обоих уровней используется локально избыточное хранилище.
13. Из списка **Disk caching** (Кэширование дисков) выберите соответствующий параметр кэширования дисков. Возможные параметры: **None** (Нет), **Read-only** (Только чтение) и **Read\write** (Чтение и запись).
14. (Необязательно.) В образ можно добавить существующий диск данных, щелкнув **+ Добавить диск данных**.  
15. Завершив настройку параметров, нажмите кнопку **Создать**.
16. После создания образа вы увидите его в виде ресурса **Image** в списке ресурсов группы ресурсов, которую вы выбрали.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Создание управляемого образа виртуальной машины с помощью PowerShell

Создание образа непосредственно из виртуальной машины гарантирует, что он будет содержать все ее диски, включая диск ОС и диски данных.


Прежде чем начать, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](/powershell/azure/overview).


1. Создайте несколько переменных. 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Убедитесь, что виртуальная машина была освобождена.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Теперь задайте для виртуальной машины состояние **Generalized**(Универсальная). 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Получите виртуальную машину. 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Создайте конфигурацию образа.

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Создайте образ.

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>Создание управляемого образа виртуального жесткого диска с помощью PowerShell

Создайте управляемый образ с помощью универсального виртуального жесткого диска ОС.


1.  Сначала задайте общие параметры.

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Остановите и освободите виртуальную машину.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Пометьте виртуальную машину как универсальную.

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Создайте образ с помощью универсального виртуального жесткого диска ОС.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Создание управляемого образа из моментального снимка с помощью PowerShell

Можно создать управляемый образ из моментального снимка виртуального жесткого диска универсальной виртуальной машины.

    
1. Создайте несколько переменных. 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Получите моментальный снимок.

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Создайте конфигурацию образа.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Создайте образ.

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 
    

## <a name="next-steps"></a>Дальнейшие действия
- Теперь вы можете [создать виртуальную машину из универсального управляемого образа](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  


