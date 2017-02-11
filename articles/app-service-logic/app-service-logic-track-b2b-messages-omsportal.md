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
ms.sourcegitcommit: b3f564e32105708ddbd9027240c897fdd8ae2ac6
ms.openlocfilehash: 673b128c628a13c4af6c73c1a2d82953aadfd45a


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
* Учетная запись интеграции. Вы можете создать [учетную запись интеграции](app-service-logic-enterprise-integration-create-integration-account.md) и включить ведение журнала. Соответствующие действия см. [здесь](app-service-logic-monitor-b2b-message.md).
* Приложение логики. Вы можете создать [приложение логики](app-service-logic-create-a-logic-app.md) и включить ведение журнала. Соответствующие действия см. [здесь](app-service-logic-monitor-your-logic-apps.md).

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Добавление решения B2B для приложений логики на портал OMS
1. На портале выберите **Больше служб**, введите в поле поиска **log analytics** и выберите **Log Analytics**
![Поиск Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png).  

2. Выберите **Log Analytics**
![Выбор Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png).

3. Выберите **OMS Portal** (Портал OMS). Откроется домашняя страница портала OMS![Обзор портала OMS](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png).

4. Выберите **Коллекция решений**  .  
![Выбор коллекции решений](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. Выберите **Logic Apps B2B** (B2B для приложений логики)
![Выбор B2B для приложений логики](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png).

6. Щелкните **Добавить**, чтобы добавить **сообщения B2B для приложений логики ** на домашнюю страницу![Выбор кнопки "Добавить"](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png).

7. Перейдите на домашнюю страницу, чтобы просмотреть **сообщения B2B для приложений логики**
![Выбор домашней страницы](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png).

8. Опубликуйте сведения об обработке сообщений. На домашней странице обновится количество сообщений![Выбор домашней страницы](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png).

9. Если выбрать **Logic Apps B2B Messages** (Сообщения B2B для приложений логики) на домашней странице, откроется страница с состояниями сообщений AS2 и X12.  Данные взяты за последний день.
![Выбор сообщений B2B для приложений логики](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

10. Если выбрать сообщения AS2 или X12 по состоянию, откроется список сообщений![Выбор состояния сообщений AS2](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png).

    ![Выбор состояния сообщений X12](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

11. Если выбрать строку в списке сообщений AS2 или X12, вы перейдете на страницу поиска журналов.  На странице поиска журналов указаны все действия с одинаковым **идентификатором запуска**
![Выбор состояния сообщения](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png).


## <a name="next-steps"></a>Дальнейшие действия
[Настраиваемая схема отслеживания](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Схема отслеживания AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Схема отслеживания X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


