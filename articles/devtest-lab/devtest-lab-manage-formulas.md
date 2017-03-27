---
title: "Управление формулами в Azure DevTest Labs для создания виртуальных машин | Документация Майкрософт"
description: "Узнайте, как изменять и удалять формулы Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Управление формулами Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

В статье [Создание формул в Azure DevTest Labs](devtest-lab-create-formulas.md#create-a-formula) описан процесс создания формулы в Azure DevTest Labs. После создания формулы ознакомьтесь с этой статьей, которая поможет вам освоить управление формулами.

## <a name="modify-a-formula"></a>Изменение формулы
Чтобы изменить формулу, выполните указанные ниже действия.

1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Щелкните **Больше служб**, а затем выберите в списке **DevTest Labs**.
3. Из списка лабораторий выберите нужную лабораторию.  
4. В колонке лаборатории выберите **Formulas (reusable bases)**(Формулы (базовые образы, используемые повторно)).
   
    ![Меню формулы](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. В колонке **Lab formulas** (Формулы лаборатории) выберите формулу, которую нужно изменить.
6. В колонке **Update formula** (Обновление формулы) внесите необходимые изменения и щелкните **Обновить**.

## <a name="delete-a-formula"></a>Удаление формулы
Чтобы удалить формулу, выполните указанные ниже действия.

1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Щелкните **Больше служб**, а затем выберите в списке **DevTest Labs**.
3. Из списка лабораторий выберите нужную лабораторию.  
4. В колонке **Параметры** лаборатории выберите **Формулы**.
   
    ![Меню формулы](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. В колонке **Lab formulas** (Формулы лаборатории) щелкните многоточие справа от формулы, которую нужно удалить.
   
    ![Меню формулы](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. В контекстном меню формулы выберите **Удалить**.
   
    ![Контекстное меню формулы](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. В диалоговом окне подтверждения удаления нажмите кнопку **Да** .

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Связанные записи в блогах
* [Custom images or formulas? (Пользовательские изображения или формулы?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Дальнейшие действия
После создания формулы, которая служит для создания виртуальных машин, можно [добавить виртуальную машину в лабораторию](devtest-lab-add-vm-with-artifacts.md).


