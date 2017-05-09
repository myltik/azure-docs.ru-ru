---
title: "Создание и передача образа виртуальной машины с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создать и передать универсальный образ Windows Server (VHD) с помощью классической модели развертывания и Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 62c428215c5bd89789ae65bd420281a2f16f33a1
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Создание и передача виртуального жесткого диска Windows Server в Azure
В этой статье показан процесс передачи собственного универсального образа виртуальной машины как виртуального жесткого диска (VHD-файла) и дальнейшего его использования для создания виртуальных машин. Дополнительные сведения о дисках и виртуальных жестких дисках в Microsoft Azure см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Вы также можете [отправить](../../virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) виртуальную машину, используя модель Resource Manager.

## <a name="prerequisites"></a>Предварительные требования
В этой статье предполагается, что у вас есть следующие компоненты.

* **Подписка Azure.** Если подписка отсутствует, можно [создать учетную запись Azure бесплатно](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell.](/powershell/azure/overview)** У вас уже установлен и настроен для использования подписки модуль Microsoft Azure PowerShell.
* **VHD-файл.** Поддерживаемая операционная система Windows, сохраненная в VHD-файле и подключенная к виртуальной машине. Проверьте, поддерживаются ли программой Sysprep роли сервера, запущенные на этом VHD. Дополнительные сведения см. в разделе [Поддержка ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > Формат VHDX не поддерживается в Microsoft Azure. Диск можно преобразовать в формат VHD с помощью диспетчера Hyper-V или [командлета Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Дополнительные сведения см. в [этом блоге](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Шаг 1. Подготовка виртуального жесткого диска
Перед отправкой виртуального жесткого диска в Azure его необходимо подготовить с помощью средства Sysprep Дополнительные сведения о программе Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx). Перед выполнением программы Sysprep выполните архивацию виртуальной машины.

В виртуальной машине, в которую была установлена операционная система, сделайте следующее:

1. Войдите в операционную систему.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.

    ![Откройте окно командной строки.](./media/createupload-vhd/sysprep_commandprompt.png)
3. Откроется диалоговое окно **Программа подготовки системы** .

   ![Запуск Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. В разделе **Программа подготовки системы** выберите **Enter System Out of Box Experience (OOBE)** (Переход в окно приветствия системы (OOBE)) и убедитесь, что установлен флажок **Подготовка к использованию**.
5. В разделе **Параметры завершения работы** выберите **Завершение работы**.
6. Нажмите кнопку **ОК**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Шаг 2. Создание учетной записи хранения и контейнера
Вам потребуется учетная запись хранения Azure, в которую можно будет отправить VHD-файл. Здесь показано, как создать учетную запись или получить нужные сведения из существующей учетной записи. Замените значения в &lsaquo;скобках&rsaquo; собственными данными.

1. Вход

    ```powershell
    Add-AzureAccount
    ```

2. Настройте свою подписку Azure.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным. Его длина должна составлять от 3 до 24 знаков. Имя может представлять собой любое сочетание букв и цифр. Необходимо также указать расположение, например "Восток США".

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Используйте новую учетную запись хранения по умолчанию.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Создайте новый контейнер.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Шаг 3. Передача VHD-файла
Для передачи VHD-файла используйте командлет [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) .

В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите следующую команду и замените переменные в &lsaquo;скобках&rsaquo; собственными данными.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Шаг 4. Добавление образа в список пользовательских образов
Чтобы добавить образ в список пользовательских образов, используйте командлет [Add-AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) .

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы можете [создать пользовательскую виртуальную машину](createportal.md) с помощью переданного образа.

