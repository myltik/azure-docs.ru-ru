---
title: "Запись образа виртуальной машины на основе универсальной виртуальной машины Azure | Документация Майкрософт"
description: "Сведения о записи образа виртуальной машины на основе универсальной виртуальной машины Azure, созданной с помощью модели развертывания Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Запись образа виртуальной машины на основе универсальной виртуальной машины Azure
В этой статье показано, как использовать Azure PowerShell для создания образа универсальной виртуальной машины Azure. Такой образ можно затем использовать для создания другой виртуальной машины. Этот образ включает в себя диск операционной системы и другие диски данных, присоединенные к виртуальной машине. Образ не включает ресурсы виртуальной сети, поэтому их придется настроить при создании виртуальной машины. 

## <a name="prerequisites"></a>Предварительные требования
У вас должна быть установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы еще не установили PowerShell, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

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


Можно также подготовить виртуальную машину Linux к использованию с помощью `sudo waagent -deprovision+user`, а затем использовать PowerShell для ее записи. Сведения об использовании интерфейса командной строки для записи виртуальной машины см. в разделе [Как обобщить и записать виртуальную машину Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия)](../linux/capture-image.md).

## <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell
1. Откройте Azure PowerShell и войдите в свою учетную запись Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Откроется всплывающее окно для ввода данных учетной записи Azure.
2. Получите идентификаторы доступных вам подписок.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Укажите соответствующую подписку с помощью идентификатора.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Отмена распределения для виртуальной машины и установка состояния "универсальный"
1. Отмените распределение ресурсов для виртуальной машины.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Состояние* виртуальной машины на портале Azure изменится с **Остановлено** на **Остановлено (освобождено)**.
2. Теперь задайте для виртуальной машины состояние **Generalized**(Универсальная). 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Проверьте состояние виртуальной машины. В разделе **OSState/generalized** для этой виртуальной машины параметр **DisplayStatus** должен иметь значение **VM generalized**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Создание образа
1. С помощью этой команды скопируйте образ виртуальной машины в целевой контейнер хранилища. Образ будет создан в той же учетной записи хранения, что и исходная виртуальная машина. Параметр `-Path` сохраняет локальную копию шаблона JSON. Параметр `-DestinationContainerName` содержит имя контейнера для хранения образов. Если такой контейнер не существует, он будет создан автоматически.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    URL-адрес образа можно получить из шаблона файла JSON. Перейдите к разделу **resources** > **storageProfile** > **osDisk** > **image** > **uri**, чтобы получить полный путь к образу. URL-адрес образа выглядит так: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    Также вы также можете проверить URI на портале. Образ копируется в контейнер **system** в вашей учетной записи хранения. 

## <a name="next-steps"></a>Дальнейшие действия
* Теперь можно [создать виртуальную машины из образа](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


