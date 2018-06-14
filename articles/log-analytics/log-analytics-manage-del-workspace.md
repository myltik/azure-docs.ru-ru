---
title: Удаление рабочей области Azure Log Analytics | Документация Майкрософт
description: Узнайте, как удалить рабочую область Log Analytics, если она была создана в личной подписке, или как изменить структуру модели рабочей области.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 084b8fa0addbe2ddeee699b58c9b0de1a6f27d99
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33938698"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Удаление рабочей области Azure Log Analytics на портале Azure
В этой статье показано, как использовать портал Azure для удаления рабочей области Log Analytics, которая больше не нужна. 

## <a name="to-delete-a-workspace"></a>Удаление рабочей области 
При удалении рабочей области Log Analytics все данные, относящиеся к ней, удалятся из службы в течение 30 дней.  Необходимо соблюдать осторожность при удалении рабочей области, так как в ней могут быть важные данные и конфигурация, удаление которых может отрицательно повлиять на работу службы. Не забывайте о других службах Azure и источниках, данные которых хранятся в Log Analytics, например:

* Application Insights
* Центр безопасности Azure
* Служба автоматизации Azure
* агенты, работающие на виртуальных машинах Windows и Linux;
* агенты, работающие на компьютерах Windows и Linux в вашей среде;
* System Center Operations Manager
* Решения для управления 

Все агенты и группы управления System Center Operations Manager, для которых настроена отправка отчетов в рабочую область, будут продолжать работу в потерянном состоянии.  Прежде чем продолжить, проведите инвентаризацию агентов, решений и других служб Azure, интегрированных с этой рабочей областью.   
 
Если вы являетесь администратором и с рабочей областью связано несколько пользователей, связь между этими пользователями и рабочей областью тоже прекратится. Если пользователи связаны с другими рабочими областями, они смогут продолжить использовать Log Analytics в этих рабочих областях. Но если они не связаны с другими рабочими областями, им необходимо создать рабочую область для использования Log Analytics. 

1. Войдите на [портал Azure](http://portal.azure.com). 
2. На портале Azure щелкните **Другие службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
3. В области подписок Log Analytics выберите рабочую область, а затем щелкните **Удалить** вверху средней области.<br><br> ![Параметр "Удалить" в области свойств рабочей области](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Когда отобразится окно с запросом подтверждения удаления рабочей области, щелкните **Да**.<br><br> ![Подтверждение удаления рабочей области](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

