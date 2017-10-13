---
title: "Настройка параметров образов Azure Marketplace в Azure DevTest Labs | Документация Майкрософт"
description: "Настройка образов Azure Marketplace, которые можно использовать при создании виртуальной машины в Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: 5f888c9d92a9164cc7d3d1aed66c29a724b365d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Настройка параметров образов Azure Marketplace в Azure DevTest Labs
DevTest Labs поддерживает создание виртуальных машин на основе образов Azure Marketplace в зависимости от того, как вы настроили образы Azure Marketplace для использования в лаборатории. В этой статье показано, как определить, какие образы Azure Marketplace (если таковые имеются) можно использовать при создании виртуальных машин в лаборатории. Благодаря этому ваша рабочая группа будет иметь доступ только к тем образам Marketplace, которые ей требуются. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Выбор разрешенных образов Azure Marketplace при создании виртуальной машины
1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Щелкните **Больше служб**, а затем выберите в списке **DevTest Labs**.
3. Из списка лабораторий выберите нужную лабораторию. 
4. В колонке лаборатории выберите **Конфигурация и политики**.
5. В колонке **Конфигурация и политики** лаборатории в разделе **Базы виртуальных машин** выберите **Образы Marketplace**.
6. Укажите, должны ли все соответствующие образы Azure Marketplace быть доступны для использования в качестве основы новой виртуальной машины. При выборе варианта **Да**в лаборатории будут разрешены все образы Azure Marketplace, соответствующие всем следующим условиям:
   
   * образ создает одну виртуальную машину **и**
   * образ использует диспетчер ресурсов Azure для подготовки виртуальных машин, **а также**
   * для образа не требуется приобретать дополнительный план лицензирования.
     
    Если не требуется разрешать никакие образы или нужно указать, какие образы можно использовать, нажмите кнопку **Нет**.
     
     ![Параметр для разрешения использования всех образов Marketplace в качестве базовых образов для виртуальных машин](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Если на предыдущем шаге вы выбрали **Нет**, то активируется флажок **Allowed images/Select all** (Разрешенные образы/выбрать все). 
   Этот параметр можно использовать вместе с полем поиска для быстрого выбора или отмены выбора всех элементов, отображаемых в списке.
   * Образы Azure Marketplace, которые необходимо разрешить для создания виртуальной машины, можно выбрать по отдельности, установив соответствующий флажок для каждого образа.
   * Ничего не выбирайте, если не хотите использовать образы Azure Marketplace в лаборатории.
   
    ![Можно указать, какие образы Azure Marketplace будут использоваться в качестве базовых образов для виртуальных машин.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Дальнейшие действия
После настройки разрешения образов Azure Marketplace при создании виртуальной машины необходимо [добавить виртуальную машину в лабораторию](devtest-lab-add-vm-with-artifacts.md).

