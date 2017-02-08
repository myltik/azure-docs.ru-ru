---
title: "Отслеживание сообщений B2B на портале OMS | Документация Майкрософт"
description: "Как отслеживать сообщения B2B на портале OMS"
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
ms.openlocfilehash: c73c057c97af9dccf25c724603c597b6792553fd


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>Отслеживание сообщений B2B на портале OMS
Взаимодействие B2B включает обмен сообщениями между двумя запущенными бизнес-процессами или приложениями. Портал OMS обеспечивает широкие возможности отслеживания сообщений B2B в веб-среде, позволяющие определить, обрабатываются ли сообщения должным образом.  Вы можете отслеживать следующее:

* количество и состояние сообщений;
* состояние подтверждений;
* сопоставление сообщений с подтверждениями;
* подробное описание ошибки для сбоев;
* возможности поиска.

## <a name="prerequisites"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Учетная запись интеграции. Вы можете создать [учетную запись интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) и включить ведение журнала. Соответствующие действия см. [здесь](logic-apps-monitor-b2b-message.md).
* Приложение логики. Вы можете создать [приложение логики](../logic-apps/logic-apps-create-a-logic-app.md) и включить ведение журнала. Соответствующие действия см. [здесь](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Добавление решения B2B для приложений логики на портал OMS

1. На портале выберите **Больше служб**, введите в поле поиска **log analytics** и выберите **Log Analytics**
![Поиск Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png).  

2. Выберите **Log Analytics**
![Выбор Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png).

3. Выберите **OMS Portal** (Портал OMS). Откроется домашняя страница портала OMS![Обзор портала OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png).

4. Выберите **Коллекция решений**  .  
![Выбор коллекции решений](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Выберите **Logic Apps B2B** (B2B для приложений логики)
![Выбор B2B для приложений логики](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png).

6. Щелкните **Добавить**, чтобы добавить **сообщения B2B для приложений логики ** на домашнюю страницу![Выбор кнопки "Добавить"](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png).

7. Перейдите на домашнюю страницу, чтобы просмотреть **сообщения B2B для приложений логики**
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png).

## <a name="tracking-data-in-oms-portal"></a>Данные отслеживания на портале OMS

1. Опубликуйте сведения об обработке сообщений. На домашней странице обновится количество сообщений![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png).

2. Если выбрать **Logic Apps B2B Messages** (Сообщения B2B для приложений логики) на домашней странице, откроется страница с состояниями сообщений AS2 и X12.  Данные взяты за последний день.
![Выбор сообщений B2B для приложений логики](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Если выбрать сообщения AS2 или X12 по состоянию, откроется список сообщений![Выбор состояния сообщений AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png).

    ![Выбор состояния сообщений X12](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

4. Если выбрать строку в списке сообщений AS2 или X12, вы перейдете на страницу поиска журналов.  На странице поиска журналов указаны все действия с одинаковым **идентификатором запуска**
![Выбор состояния сообщения](media/logic-apps-track-b2b-messages-omsportal/logsearch.png).

## <a name="queries-in-oms-portal"></a>Запросы на портале OMS

На странице поиска можно создать запрос, а затем во время поиска отфильтровать результаты с помощью элементов управления аспектами.

### <a name="how-to-create-a-query"></a>Как создать запрос

1. На странице поиска в журнале создайте запрос и щелкните **Сохранить**.  [Здесь](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md) описывается, как создать запрос ![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png).

2. Откроется диалоговое окно **Сохранить условия поиска**.  Укажите **имя**, **категорию** и нажмите кнопку **Сохранить** .  
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Чтобы просмотреть запрос, щелкните **Избранное**  .  
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>Как использовать сохраненный запрос

1. На странице поиска в журнале щелкните **Избранное**, чтобы просмотреть сохраненные запросы.  Выбрав один из них, вы сможете получить результаты этого запроса. ![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Дальнейшие действия
[Настраиваемая схема отслеживания](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Схема отслеживания AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Схема отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Дополнительные сведения о Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO3-->


