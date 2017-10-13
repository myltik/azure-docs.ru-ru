---
title: "Создание пользовательского образа Azure DevTest Labs из виртуальной машины | Документация Майкрософт"
description: "Сведения о создании пользовательского образа в Azure DevTest Labs из подготовленной виртуальной машины с использованием портала Azure"
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
ms.openlocfilehash: 9d2dcf7164985508d691e8a0c123efaf3b8aa19a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-image-from-a-vm"></a>Создание пользовательского образа из виртуальной машины

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Вы можете создать пользовательский образ из подготовленной виртуальной машины, а затем использовать его для создания идентичных виртуальных машин. Ниже описано, как создать пользовательский образ на основе виртуальной машины.

1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Другие службы**, а затем выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.  

1. В колонке лаборатории выберите **My virtual machines**(Мои виртуальные машины).
 
1. В колонке **My virtual machines** (Мои виртуальные машины) выберите виртуальную машину, на основе которой будет создан пользовательский образ.

1. В колонке виртуальной машины выберите **Create custom image (VHD)**(Создать пользовательский образ (VHD)).

    ![Пункт меню "Создание пользовательского образа"](./media/devtest-lab-create-template/create-custom-image.png)

1. В колонке **Create image** (Создание образа) введите имя и описание нового пользовательского образа. Эти данные отображаются в списке базовых образов при создании виртуальной машины.

    ![Колонка "Создание пользовательского образа"](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Укажите, была ли выполнена на виртуальной машине программа sysprep. Если программа sysprep не была запущена на виртуальной машине, укажите, нужно ли ее запускать при создании виртуальной машины из этого пользовательского образа.

1. Нажмите кнопку **ОК** , чтобы создать пользовательский образ.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Связанные записи в блогах

- [Custom images or formulas? (Пользовательские изображения или формулы?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Копирование пользовательских образов между лабораториями для разработки и тестирования Azure)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Дальнейшие действия

- [Добавление виртуальной машины с артефактами в лабораторию в Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md)
