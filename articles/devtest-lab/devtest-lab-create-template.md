---
title: "Создание пользовательского образа Azure DevTest Labs из VHD-файла | Документация Майкрософт"
description: "Сведения о создании пользовательского образа в Azure DevTest Labs из VHD-файла с использованием портала Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: v-craic
ms.openlocfilehash: d1f1b9948fb591484c107818a01e141932effbba
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Создание пользовательского образа из VHD-файла

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Ниже приведены инструкции по созданию пользовательского образа из VHD-файла с помощью портала Azure.

1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.  

1. В главной области лаборатории выберите **Конфигурация и политики**. 

1. На панели **Конфигурация и политики** выберите **Пользовательские образы**.

1. На панели **Пользовательские образы** выберите **+ Добавить**.

    ![Добавить пользовательский образ](./media/devtest-lab-create-template/add-custom-image.png)

1. Введите имя пользовательского образа. Это имя отображается в списке базовых образов при создании виртуальной машины.

1. Введите описание пользовательского образа. Это описание отображается в списке базовых образов при создании виртуальной машины.

1. Для параметра **Тип ОС** выберите значение **Windows** или **Linux**.

    - Если выбираете **Windows**, укажите с помощью флажка, выполняется ли на компьютере *sysprep*. 
    - Если выбираете **Linux**, укажите с помощью флажка, выполняется ли на компьютере *deprovision*. 

1. В раскрывающемся меню выберите **VHD**. Это виртуальный жесткий диск, который будет использоваться для создания пользовательского образа. При необходимости выберите параметр **Отправить VHD с помощью PowerShell**.

1. Кроме того, вы можете указать имя, предложение и издателя плана, если образ, используемый для создания пользовательского образа, не опубликован корпорацией Майкрософт.

1. Нажмите кнопку **ОК** , чтобы создать пользовательский образ.

Через несколько минут пользовательский образ создается и сохраняется в учетной записи хранения лаборатории. Когда пользователь лаборатории создает новую виртуальную машину, этот образ доступен в списке базовых.

![Пользовательский образ в списке базовых образов](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Связанные записи в блогах

- [Custom images or formulas? (Пользовательские изображения или формулы?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Копирование пользовательских образов между лабораториями для разработки и тестирования Azure)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Дополнительная информация

- [Добавление виртуальной машины с артефактами в лабораторию в Azure DevTest Labs](./devtest-lab-add-vm.md)
