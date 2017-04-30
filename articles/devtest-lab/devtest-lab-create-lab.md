---
title: "Создание лаборатории в Azure DevTest Labs | Документация Майкрософт"
description: "Создание лаборатории в Azure DevTest Labs для виртуальных машин"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 75188a8f74b08d28b92259348626d5734ed4ce6c
ms.lasthandoff: 04/20/2017


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Создание лаборатории в лаборатории для разработки и тестирования Azure
## <a name="prerequisites"></a>Предварительные требования
Чтобы создать лабораторию, вам потребуется:

* Подписка Azure. Дополнительные сведения о вариантах приобретения Azure см. на страницах [Как приобрести Azure](https://azure.microsoft.com/pricing/purchase-options/) и [Бесплатный ознакомительный период в один месяц](https://azure.microsoft.com/pricing/free-trial/). Для создания лаборатории необходимо быть владельцем подписки.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Создание лаборатории в Azure DevTest Labs
Ниже показано, как с помощью портала Azure создать лабораторию в Azure DevTest Labs. 

1. Выполните вход на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Щелкните **Другие службы**, а затем выберите в списке **DevTest Labs**.
3. В колонке **DevTest Labs** нажмите кнопку **Добавить**.
   
    ![Добавление лаборатории](./media/devtest-lab-create-lab/add-lab-button.png)
4. В колонке **Создание лаборатории для разработки и тестирования** сделайте следующее:
   
   1. Введите **имя лаборатории** .
   2. Выберите **подписку** , которую необходимо связать с лабораторией.
   3. Выберите **расположение** , в котором будет храниться лаборатория.
   4. Выберите **Автоматическое завершение работы** , чтобы включить и определить параметры автоматического завершения работы всех виртуальных машин в лаборатории. 
   5. Выберите **Закрепить на панели мониторинга**, чтобы отобразить ярлык лаборатории на панели мониторинга портала.
   6. Выберите **Параметры автоматизации**, чтобы получить шаблоны Azure Resource Manager для автоматизации настройки. 
   7. Нажмите кнопку **Создать**.
    
    ![Колонка создания лаборатории](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Дальнейшие действия
Создав лабораторию, можно выполнить дальнейшие действия, приведенные ниже.

* [Безопасный доступ к лаборатории](devtest-lab-add-devtest-user.md).
* [Определение политик лаборатории](devtest-lab-set-lab-policy.md).
* [Создание шаблона лаборатории](devtest-lab-create-template.md).
* [Создание пользовательских артефактов для виртуальных машин](devtest-lab-artifact-author.md).
* [Добавление виртуальной машины с артефактами в лабораторию](devtest-lab-add-vm-with-artifacts.md).


