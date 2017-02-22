---
title: "Создание пользовательского образа Azure DevTest Labs из VHD-файла | Документация Майкрософт"
description: "Сведения о создании пользовательского образа в Azure DevTest Labs из VHD-файла с использованием портала Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 38d70ed302eeab912ce4fe33272f85e96f1b1eda
ms.openlocfilehash: 9983ea9b847f44ed18a6169a4bdb224b63626a64


---

# <a name="create-a-custom-image-from-a-vhd-file"></a>Создание пользовательского образа из VHD-файла

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Ниже приведены инструкции по созданию пользовательского образа из VHD-файла с помощью портала Azure.

1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Другие службы**, а затем выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.  

1. В колонке лаборатории выберите **Конфигурация**. 

1. В колонке **Конфигурация** лаборатории выберите **Custom images (VHDs)** (Пользовательские образы (VHD)).

1. В колонке **Custom images** (Пользовательские образы) выберите **+Add** (+ Добавить).

    ![Добавить пользовательский образ](./media/devtest-lab-create-template/add-custom-image.png)

1. Введите имя пользовательского образа. Это имя отображается в списке базовых образов при создании виртуальной машины.

1. Введите описание пользовательского образа. Это описание отображается в списке базовых образов при создании виртуальной машины.

1. Выберите **VHD**.

1. В колонке **VHD** выберите нужный VHD-файл.

1. Нажмите кнопку **ОК**, чтобы закрыть колонку **VHD**.

1. Щелкните **Конфигурация ОС**.

1. На вкладке **Конфигурация ОС** выберите **Windows** или **Linux**.

1. Если выбран вариант **Windows** , то с помощью флажка укажите, была ли запущена программа *Sysprep* . 

1. Нажмите кнопку **ОК**, чтобы закрыть колонку **Конфигурация ОС**.

1. Нажмите кнопку **ОК** , чтобы создать пользовательский образ.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Связанные записи в блогах

- [Custom images or formulas? (Пользовательские изображения или формулы?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Копирование пользовательских образов между лабораториями для разработки и тестирования Azure)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Дальнейшие действия

- [Добавление виртуальной машины с артефактами в лабораторию в Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md)



<!--HONumber=Jan17_HO2-->


