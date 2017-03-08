---
title: "Отправка VHD-файла в Azure Labs DevTest с помощью PowerShell | Документация Майкрософт"
description: "Отправка VHD-файла в учетную запись хранения лаборатории с помощью PowerShell"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: 379544e6592e933e510308bb0ae7193542c099f2


---

# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Отправка VHD-файла в учетную запись хранения лаборатории с помощью PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

В Azure DevTest Labs с помощью VHD-файлов можно создать пользовательские образы, используемые для подготовки виртуальных машин. Ниже приведены пошаговые инструкции по передаче VHD-файла в учетную запись хранения лаборатории с помощью PowerShell. После отправки VHD-файла переходите к разделу [Дальнейшие действия](#next-steps), где перечислены несколько статей, в которых описывается создание пользовательского образа из загруженного VHD-файла. См. дополнительные сведения [о дисках и виртуальных жестких дисках для виртуальных машин Azure](../storage/storage-about-disks-and-vhds-linux.md).

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Ниже приведены пошаговые инструкции по отправке VHD-файла в Azure DevTest Labs с помощью PowerShell. 

1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Другие службы**, а затем выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.  

1. В колонке лаборатории выберите **Конфигурация**. 

1. В колонке **Configuration** (Конфигурация) лаборатории выберите **Custom images (VHDs)** (Пользовательские образы (VHD)).

1. В колонке **Custom images** (Пользовательские образы) выберите **+Add** (+ Добавить). 

1. В колонке **Custom images** (Пользовательские образы) выберите **VHD**.

1. В колонке **VHD** щелкните **Upload a VHD file using PowerShell** (Отправить VHD-файл с помощью PowerShell).

    ![Отправка VHD с помощью PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. В колонке **Upload an image using PowerShell** (Отправка образа с помощью PowerShell) скопируйте созданный сценарий PowerShell в текстовый редактор.

1. Измените параметр **LocalFilePath** командлета **Add-AzureVhd**, чтобы указать расположение VHD-файла, который необходимо передать.

1. В командной строке PowerShell выполните командлет **Add-AzureVhd** (с измененным параметром **LocalFilePath**).

> [!WARNING] 
> 
> В зависимости от размера VHD-файла и скорости подключения процесс передачи файла может занять длительное время.

## <a name="next-steps"></a>Дальнейшие действия

- [Управление пользовательскими образами Azure DevTest Labs для создания виртуальных машин](devtest-lab-create-template.md)
- [Создание пользовательского образа из VHD-файла с помощью PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)



<!--HONumber=Feb17_HO3-->


