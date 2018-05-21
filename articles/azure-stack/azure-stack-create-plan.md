---
title: Создание плана в Azure Stack | Документация Майкрософт
description: Создание администратором служб облака, которое позволяет подписчикам подготавливать виртуальные машины.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Создание плана в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

[Планы](azure-stack-key-features.md) — это группы, содержащие одну или несколько служб. Вы как поставщик создаете планы, чтобы предложить их пользователям. В свою очередь, пользователи подписываются на предложения, чтобы использовать содержащиеся в них планы и службы. В этом примере показано, как создать план, включающий в себя поставщики вычислительных и сетевых ресурсов, а также ресурсов хранилища. Этот план даст подписчикам возможность подготавливать виртуальные машины.

1. Войдите на портал администратора Azure Stack (https://adminportal.local.azurestack.external).

2. Чтобы создать план и предложение, на которое могут подписываться пользователи, щелкните **Создать** > **Offers + Plans** (Предложения + планы) > **План**.  
   ![Выбор плана](media/azure-stack-create-plan/select-plan.png)

3. В области **Создать план** заполните поля **Отображаемое имя** и **Имя ресурса**. Отображаемое имя — это понятное имя плана, которое отображается для пользователей. Только администратор может видеть имя ресурса, которое администраторы используют для работы с планом в качестве ресурса Azure Resource Manager.  
   ![Ввод сведений](media/azure-stack-create-plan/plan-name.png)

4. Создайте **группу ресурсов** (или выберите имеющуюся), которая послужит контейнером для плана.  
   ![Указание группы ресурсов](media/azure-stack-create-plan/resource-group.png)

5. Щелкните **Службы**, а затем установите флажки для служб **Microsoft.Compute**, **Microsoft.Network** и **Microsoft.Storage**. Затем нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию. Флажки появляются при наведении указателя мыши на каждый параметр.  
   ![Выбор служб](media/azure-stack-create-plan/services.png)

6. Щелкните **Квоты**, выберите **Microsoft.Storage (local)** (Microsoft.Storage (локальная)), а затем выберите квоту по умолчанию или щелкните **Создание квоты**, чтобы настроить квоту.  
   ![Квоты](media/azure-stack-create-plan/quotas.png)

7. При создании квоты введите **имя** для квоты, задайте значения квоты, а затем нажмите кнопку **ОК**. Область **Создание квоты** закроется.
   ![Создание квоты](media/azure-stack-create-plan/new-quota.png)

   Выберите созданную новую квоту. При выборе квоты она назначается, а область выбора закрывается.  
   ![Назначение квоты](media/azure-stack-create-plan/assign-quota.png)

8. Повторите шаги 6 и 7, чтобы создать и назначить квоты для служб **Microsoft.Network (local)** (Microsoft.Network (локальная)) и **Microsoft.Compute (local)** (Microsoft.Compute (локальная)).  Когда квота будет назначена всем трем службам, отобразится примерно следующее:  
   ![Завершение назначения квот](media/azure-stack-create-plan/all-quotas-assigned.png)

9. На странице **Квоты** нажмите кнопку **ОК**, а затем в области **Создать план** нажмите кнопку **Создать** для создания плана.  
    ![Создание плана](media/azure-stack-create-plan/create.png)
10. Для просмотра нового плана щелкните **Все ресурсы**, а затем найдите план и щелкните его имя. В длинном списке ресурсов план можно найти по имени с помощью **поиска**.  
   ![Проверка плана](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>Дополнительная информация
[Создание предложения](azure-stack-create-offer.md)
