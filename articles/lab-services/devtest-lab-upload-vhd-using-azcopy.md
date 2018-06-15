---
title: Отправка VHD-файла в Azure Labs DevTest с помощью AzCopy | Документация Майкрософт
description: Отправка VHD-файла в учетную запись хранения лаборатории с помощью AzCopy
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e35686e7ba7c2e88d62930082d39856673a661b6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782453"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Отправка VHD-файла в учетную запись хранения лаборатории с помощью AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

В Azure DevTest Labs с помощью VHD-файлов можно создать пользовательские образы, используемые для подготовки виртуальных машин. Ниже приведены пошаговые инструкции по отправке VHD-файла в учетную запись хранения лаборатории с помощью служебной программы командной строки AzCopy. После отправки VHD-файла переходите к разделу [Дальнейшие действия](#next-steps), где перечислены несколько статей, в которых описывается создание пользовательского образа из загруженного VHD-файла. Дополнительные сведения о дисках и виртуальных жестких дисках в Azure см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин Azure](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> AzCopy является служебной программой командной строки только для Windows.

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Ниже приведены пошаговые инструкции по отправке VHD-файла в Azure DevTest Labs с помощью [AzCopy](http://aka.ms/downloadazcopy). 

1. Узнайте имя учетной записи хранения лаборатории с помощью портала Azure.

1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.  

1. В колонке лаборатории выберите **Конфигурация**. 

1. В колонке **Configuration** (Конфигурация) лаборатории выберите **Custom images (VHDs)** (Пользовательские образы (VHD)).

1. В колонке **Custom images** (Пользовательские образы) выберите **+Add** (+ Добавить). 

1. В колонке **Custom images** (Пользовательские образы) выберите **VHD**.

1. В колонке **VHD** щелкните **Upload a VHD file using PowerShell** (Отправить VHD-файл с помощью PowerShell).

    ![Отправка VHD с помощью PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. В колонке **Upload an image using PowerShell** (Отправка образа с помощью PowerShell) отображается вызов командлета **Add-AzureVhd**. Первый параметр (*Назначение*) содержит URI для контейнера больших двоичных объектов (*отправки*) в следующем формате:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Запишите полный URI, так как он понадобится вам дальше.

1. Отправьте VHD-файл с помощью AzCopy.
 
1. [Скачайте и установите последнюю версию AzCopy](http://aka.ms/downloadazcopy).

1. Откройте окно командной строки и перейдите к каталогу установки AzCopy. При необходимости можно добавить место установки AzCopy к системному пути. По умолчанию AzCopy устанавливается в следующий каталог:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Используя ключ учетной записи и URI контейнера больших двоичных объектов, выполните следующую команду в командной строке. Значение *vhdFileName* должно быть заключено в кавычки. В зависимости от размера VHD-файла и скорости подключения процесс передачи файла может занять длительное время.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Управление пользовательскими образами Azure DevTest Labs для создания виртуальных машин](devtest-lab-create-template.md)
- [Создание пользовательского образа из VHD-файла с помощью PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)