---
title: "Приступая к работе с Azure Advisor | Документация Майкрософт"
description: "Приступая к работе с Azure Advisor"
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
translationtype: Human Translation
ms.sourcegitcommit: 14e9e494d6e824964b9164ff36d9938341e244be
ms.openlocfilehash: 7adaa1a457e19c66bdc31a01f4e04b7911e58062

---

# <a name="get-started-with-azure-advisor"></a>Приступая к работе с Azure Advisor

Узнайте, как воспользоваться Advisor с помощью портала Azure и как получить, реализовать, найти и обновить рекомендации.

## <a name="get-advisor-recommendations"></a>Получение рекомендаций Advisor

1. Войдите на [портал Azure](https://portal.azure.com).
2. В области навигации слева щелкните **Больше служб**, затем прокрутите вниз область меню служб до раздела **Мониторинг и управление** и щелкните **Помощник по Azure**. Откроется панель мониторинга Advisor.

  ![Как воспользоваться Azure Advisor с помощью портала Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. На панели мониторинга Advisor выберите подписку, для которой вы хотите получать рекомендации. Панель мониторинга Advisor отображает персонализированные рекомендации для выбранной подписки. 
4. Чтобы получить рекомендации в определенной категории, щелкните одну из вкладок: **Высокая доступность**, **Безопасность**, **Производительность** или **Стоимость**.
 
> [!NOTE]
> Azure Advisor создает рекомендации для подписок, для которых вам назначена роль **владельца**, **участника** или **читателя**.

  ![Панель мониторинга Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>Получение сведений о рекомендации Advisor и ее реализация

Колонка **Рекомендация** в Advisor содержит дополнительные сведения о рекомендации. 

1. Войдите на [портал Azure](https://portal.azure.com) и запустите [Azure Advisor](https://aka.ms/azureadvisordashboard).
2. На панели мониторинга **Рекомендации помощника** щелкните **Получить рекомендации**.
3. В списке щелкните рекомендацию, дополнительные сведения о которой вы хотите просмотреть. Откроется колонка этой рекомендации.
4. В колонке рекомендации просмотрите сведения о действиях, которые необходимо выполнить, чтобы устранить потенциальную проблему или воспользоваться возможностью сократить расходы. 
  
  ![Пример действия из рекомендации Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Поиск рекомендаций Advisor

Можно найти рекомендации для определенной подписки или группы ресурсов. Можно также выполнять поиск рекомендаций по состоянию.

1. Войдите на портал Azure и запустите Azure Advisor.
2. Выполните поиск рекомендаций с помощью фильтра по подписке, группе ресурсов и состоянию рекомендации (**Активно** или **Отложена**).
3. Щелкните **Получить рекомендации**, чтобы получить список рекомендаций Advisor в соответствии с заданными фильтрами поиска.

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>Откладывание рекомендаций Advisor

1. Войдите на портал Azure и запустите Azure Advisor.
2. Щелкните **Получить рекомендации**, затем в списке щелкните рекомендацию.
3. В колонке **Рекомендация** щелкните **Отложить**.  Можно указать период времени, на который откладывается рекомендация, или выбрать значение **Никогда**, чтобы закрыть ее.

  ![Пример действия из рекомендации Advisor](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше об Advisor, ознакомьтесь со следующими материалами.
-  [Общие сведения об Azure Advisor](advisor-overview.md)
-  [Рекомендации Azure Advisor по высокой доступности](advisor-high-availability-recommendations.md)
-  [Рекомендации Azure Advisor по безопасности](advisor-security-recommendations.md)
-  [Рекомендации Azure Advisor по производительности](advisor-performance-recommendations.md)
-  [Рекомендации Azure Advisor по затратам](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->


