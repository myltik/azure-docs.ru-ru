---
title: "Отслеживание сообщений B2B на портале Operations Management Suite с помощью запроса | Документация Майкрософт"
description: "Сведения об отслеживании сообщений B2B на портале Operations Management Suite с помощью запроса."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: df6beaaa5de45acfa850049721797fc43f4c9ed7


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Отслеживание сообщений B2B на портале Operations Management Suite с помощью запроса
Для отслеживания сообщений типа "бизнес — бизнес" (B2B) на портале Operations Management Suite можно создать запрос, который будет фильтровать данные для конкретного контрольного номера обмена.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнять отладку и получать более подробные диагностические данные, включите диагностику в [учетной записи интеграции](logic-apps-monitor-b2b-message.md) для [приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) с соединителями X12. Затем выполните действия по [публикации диагностических данных](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) на портале Operations Management Suite.

## <a name="to-create-a-query-to-search-for-a-specific-interchange-control-number"></a>Создание запроса для поиска конкретного контрольного номера обмена

1. На начальной странице выберите **Поиск по журналу**.  
![Выбор поиска в журналах](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. В поле поиска введите **Type="AzureDiagnostics"**. Чтобы отфильтровать данные, щелкните **Добавить**.  
![Выбор запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Введите **interchange**, выберите **event_record_messageProperties_interchangeControlNumber_s**, а затем щелкните **Добавить**.  
![Добавление фильтра](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Выберите контрольный номер, по которому необходимо отфильтровать данные, а затем щелкните **Применить**.  
![Поиск по контрольному номеру](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Вы можете найти запрос, созданный для фильтрации данных по выбранному контрольному номеру.   
![Поиск запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Введите имя запроса и сохраните его.   
![Сохранение запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Чтобы просмотреть запрос и использовать его для поиска в будущем, выберите **Избранное**.  
![Просмотр запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. Запрос можно изменить для поиска нового контрольного номера обмена.  
![Изменение запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [настраиваемых схемах отслеживания](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Дополнительные сведения о [схемах отслеживания AS2](logic-apps-track-integration-account-as2-tracking-schemas.md).    
* Дополнительные сведения о [схемах отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md).  
* Дополнительные сведения о [Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md).



<!--HONumber=Jan17_HO3-->


