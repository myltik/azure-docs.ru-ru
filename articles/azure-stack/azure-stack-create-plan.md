---
title: "Создание плана в Azure Stack | Документация Майкрософт"
description: "Создание администратором служб облака, которое позволяет подписчикам подготавливать виртуальные машины."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Создание плана в Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

[Планы](azure-stack-key-features.md) — это группы, содержащие одну или несколько служб. Вы как поставщик создаете планы, чтобы предложить их пользователям. В свою очередь, пользователи подписываются на предложения, чтобы использовать содержащиеся в них планы и службы. В этом примере показано, как создать план, включающий в себя поставщики вычислительных и сетевых ресурсов, а также ресурсов хранилища. Этот план даст подписчикам возможность подготавливать виртуальные машины.

1. Войдите на портал администратора Azure Stack (https://adminportal.local.azurestack.external). Введите учетные данные для учетной записи, созданной на шаге 5 раздела [запуска сценария PowerShell](azure-stack-run-powershell-script.md).

2. Чтобы создать план и предложение, на которое могут подписываться пользователи, щелкните **Создать** > **Tenant Offers + Plans** (Предложения клиента + планы) > **План**.

   ![](media/azure-stack-create-plan/image01.png)
3. В колонке **Создать план** заполните поля **Отображаемое имя** и **Имя ресурса**. Отображаемое имя — это понятное имя плана, отображаемое для пользователя. Только администратор может видеть имя ресурса. Это имя, которое администраторы используют для работы с планом в качестве ресурса диспетчера ресурсов Azure.

   ![](media/azure-stack-create-plan/image02.png)
4. Создайте **группу ресурсов** (или выберите имеющуюся), которая послужит контейнером для плана.

   ![](media/azure-stack-create-plan/image02a.png)
5. Щелкните **Службы**, выберите **Microsoft.Compute**, **Microsoft.Network** и **Microsoft.Storage**, а затем нажмите кнопку **Выбрать**.

   ![](media/azure-stack-create-plan/image03.png)
6. Щелкните **Квоты**, выберите **Microsoft.Storage (локальное)**, а затем выберите квоту по умолчанию или щелкните **Create new quota** (Создать квоту), чтобы настроить квоту.

   ![](media/azure-stack-create-plan/image04.png)
7. При создании квоты введите имя для квоты, задайте значения квоты, а затем нажмите кнопку **ОК** и выберите имя новой квоты.

   ![](media/azure-stack-create-plan/image06.png)
8. Щелкните **Microsoft.Network (локальное)**, а затем выберите квоту по умолчанию или щелкните **Create new quota** (Создать квоту), чтобы настроить квоту.

    ![](media/azure-stack-create-plan/image07.png)
9. При создании квоты введите имя для квоты, задайте значения квоты, а затем нажмите кнопку **ОК** и выберите имя новой квоты.

    ![](media/azure-stack-create-plan/image08.png)
10. Щелкните **Microsoft.Compute (локальное)**, а затем выберите квоту по умолчанию или щелкните **Create new quota** (Создать квоту), чтобы настроить квоту.

    ![](media/azure-stack-create-plan/image09.png)
11. При создании квоты введите имя для квоты, задайте значения квоты, а затем нажмите кнопку **ОК** и выберите имя новой квоты.

    ![](media/azure-stack-create-plan/image10.png)
12. В колонке **Квоты** нажмите кнопку **ОК**, а затем в колонке **New Plan** (Создать план) нажмите кнопку **Создать** для создания плана.

    ![](media/azure-stack-create-plan/image11.png)
13. Для просмотра нового плана щелкните **Все ресурсы**, а затем найдите план и щелкните его имя.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Дальнейшие действия
[Создание предложения](azure-stack-create-offer.md)
