---
title: "Создание пользовательского образа Azure DevTest Labs из виртуальной машины | Документация Майкрософт"
description: "Сведения о создании пользовательского образа в Azure DevTest Labs из подготовленной виртуальной машины с использованием портала Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: v-craic
ms.openlocfilehash: dc315bcc625ea98244bb5804ce6ff1c13d0ec7b1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>Создание пользовательского образа из виртуальной машины

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Вы можете создать пользовательский образ из подготовленной виртуальной машины, а затем использовать его для создания идентичных виртуальных машин. Ниже описано, как создать пользовательский образ на основе виртуальной машины.

1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.  

1. На главной странице лаборатории выберите **Мои виртуальные машины**.
 
1. На панели **Мои виртуальные машины** выберите виртуальную машину, на основе которой будет создан пользовательский образ.

1. На панели управления виртуальной машины выберите **Create custom image (VHD)** (Создать пользовательский образ (VHD)).

    ![Пункт меню "Создание пользовательского образа"](./media/devtest-lab-create-template/create-custom-image.png)

1. На панели **Пользовательский образ** введите имя и описание нового пользовательского образа. Эти данные отображаются в списке базовых образов при создании виртуальной машины.

    ![Панель создания пользовательского образа](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Укажите, была ли выполнена на виртуальной машине программа sysprep. Если программа sysprep не была запущена на виртуальной машине, укажите, нужно ли ее запускать при создании виртуальной машины из этого пользовательского образа.

1. Нажмите кнопку **ОК** , чтобы создать пользовательский образ.

Через несколько минут пользовательский образ создается и сохраняется в учетной записи хранения лаборатории. Когда пользователь лаборатории создает новую виртуальную машину, этот образ доступен в списке базовых.

![Пользовательский образ в списке базовых образов](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Связанные записи в блогах

- [Custom images or formulas? (Пользовательские изображения или формулы?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Копирование пользовательских образов между лабораториями для разработки и тестирования Azure)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Дополнительная информация

- [Добавление виртуальной машины с артефактами в лабораторию в Azure DevTest Labs](devtest-lab-add-vm.md)
