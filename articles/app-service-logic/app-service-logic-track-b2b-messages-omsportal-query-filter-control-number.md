---
title: "Отслеживание сообщений B2B на портале OMS | Документация Майкрософт"
description: "Как отслеживать сообщения B2B на портале OMS"
author: padmavc
manager: erikre
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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>Создание запроса на портале OMS 
Указания по созданию запроса, который фильтрует данные по конкретному контрольному номеру обмена.

## <a name="prerequisites"></a>Предварительные требования

Включите диагностику [учетной записи интеграции](app-service-logic-monitor-b2b-message.md) и [приложений логики](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) с соединителями X12, чтобы получать более подробные сведения и расширить возможности отладки.  Следуйте [этим](app-service-logic-track-b2b-messages-omsportal.md) указаниям, чтобы опубликовать диагностические данные на портале OMS.

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>Создание запроса для поиска данных по конкретному контрольному номеру обмена

1. На домашней странице выберите **Поиск по журналу**.  
![Выбор поиска в журналах](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. В окне поиска введите **Type="AzureDiagnostics"**, чтобы извлечь все данные.  Щелкните **Добавить**, чтобы отфильтровать данные.  
![Выбор запроса](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Введите **interchange**, выберите **event_record_messageProperties_interchangeControlNumber_s** и щелкните **Добавить**.  
![Выбор контрольного номера](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Выберите контрольный номер, по которому вы хотите от фильтровать данные, и щелкните **Применить**.  
![Выбор контрольного номера](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Вы можете найти запрос, созданный для фильтрации данных по выбранному контрольному номеру.   
![Выбор контрольного номера](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Присвойте имя запросу и сохраните его.   
![Выбор контрольного номера](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. Выберите **Избранное**, чтобы просмотреть запрос и использовать его для поиска в будущем.  
![Выбор контрольного номера](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. Этот запрос можно изменить для поиска нового контрольного номера обмена.  
![Выбор контрольного номера](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>Дальнейшие действия
[Настраиваемая схема отслеживания](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Схема отслеживания AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Схема отслеживания X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Дополнительные сведения о Пакете интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


