---
title: Перезагрузка виртуальной машины в лаборатории в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как перезагрузить виртуальную машину в Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 24a17ce09bee1097b0418ad4e20990d359b3e084
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781573"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Перезагрузка виртуальной машины в лаборатории в Azure DevTest Labs
Перезапустить виртуальную машину в DevTest Labs можно быстро и легко с помощью действий, описанных в этой статье. Перед перезапуском виртуальной машины рассмотрите следующее:

- Для включения функции перезапуска необходимо запустить виртуальную машину.
- Если пользователь подключен к работающей виртуальной машине при выполнении перезагрузки, необходимо повторно подключиться к виртуальной машине после того, как она начнет резервное копирование.
- Если артефакт применяется при перезагрузке виртуальной машины, появится предупреждение "Невозможно применить артефакт". 

    ![Предупреждение о применении артефактов во время перезапуска](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Если виртуальная машина приостановилась во время применения артефакта, в качестве потенциального способа решить проблему можно использовать функцию перезапуска виртуальной машины.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Действия по перезагрузке виртуальной машины в лаборатории в Azure DevTest Labs
1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.
1. Из списка лабораторий выберите ту, в которой находится виртуальная машина, которую необходимо перезагрузить.  
1. На левой панели выберите **My virtual machines** (Мои виртуальные машины). 
1. Из списка выберите запущенную виртуальную машину.
1. В верхней части панели управления виртуальной машины выберите **Перезагрузить**.  

    ![Кнопка "Перезагрузка виртуальной машины"](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Следите за состоянием перезагрузки, выбрав значок **Уведомления** в верхнем правом углу окна.

    ![Просмотр состояния перезагрузки виртуальной машины](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Запущенную виртуальную машину можно также перезагрузить, выбрав кнопку с многоточием (...) в списке **My virtual machines** (Мои виртуальные машины).

![Перезапуск виртуальной машины с помощью кнопки с многоточием (...)](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Дополнительная информация
* После перезагрузки виртуальной машины можно заново подключиться к ней, выбрав **Подключиться** на соответствующей панели управления.
* Изучите [коллекцию шаблонов быстрого запуска Azure Resource Manager для DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
