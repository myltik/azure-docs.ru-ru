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
ms.sourcegitcommit: 53195091ac4b93ed94f432990c84c407615fc03e
ms.openlocfilehash: 9c3855c7fce5a9f38424f0bb6cd03f7a2c8d36be


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

1. На портале выберите **Больше служб**, введите в поле поиска **log analytics** и выберите **Log Analytics** .  
![Поиск Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Выберите запись **Log Analytics**.  
![Выбор записи Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Выберите **OMS Portal** (Портал OMS). Откроется домашняя страница портала OMS.   
![Обзор портала OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Выберите **Коллекция решений**  .  
![Выбор коллекции решений](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Выберите **Logic Apps B2B**   .  
![Выбор Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Нажмите кнопку **Добавить**, чтобы добавить элемент **Logic Apps B2B Messages** (Сообщения B2B для Logic Apps) на домашнюю страницу.  
![Выбор кнопки "Добавить"](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. Перейдите на домашнюю страницу, чтобы просмотреть **Logic Apps B2B Messages**  (Сообщения B2B для Logic Apps).  
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>Данные отслеживания на портале OMS

1. Опубликуйте сведения об обработке сообщений. На домашней странице обновится количество сообщений.   
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Если выбрать **Logic Apps B2B Messages** (Сообщения B2B для Logic Apps) на домашней странице, то откроется страница с состояниями сообщений AS2 и X12.  Данные взяты за последний день.
![Выбор сообщений B2B для приложений логики](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)



3. Если выбрать сообщения AS2 или X12 по состоянию, то откроется список сообщений.   
![Выбор состояния сообщений AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

| Свойство | Описание |
| --- | --- |
| Отправитель | Гостевой партнер, настроенный в параметрах получения, или главный партнер, настроенный в параметрах отправки, для соглашения AS2. |
| Получатель | Главный партнер, настроенный в параметрах получения, или гостевой партнер, настроенный в параметрах отправки, для соглашения AS2. |
| приложение логики; | Приложение логики, в котором настроены действия AS2. |
| Состояние | Состояние сообщения AS2. Success (Успешно) = получено или отправлено нормальное сообщение AS2, MDN не настроено; Success (Успешно) = получено или отправлено нормальное сообщение AS2, MDN настроено, получено или отправлено MDN; Failed (Сбой) = получено недопустимое сообщение AS2, MDN не настроено; Pending (Ожидание) = получено или отправлено нормальное сообщение AS2, MDN настроено и ожидается функциональное подтверждение. |
| Ack | Состояние сообщения MDN. |
| Направление | Направление сообщения AS2. |
| Идентификатор корреляции | Идентификатор для корреляции всех триггеров и действий в приложении логики. |
| Идентификатор сообщения |  Идентификатор сообщения AS2 из заголовков сообщения AS2. |
| Timestamp | Время обработки сообщения действием AS2. |
|  |  |


![Выбор состояния сообщений X12](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

| Свойство | Описание |
| --- | --- |
| Отправитель | Гостевой партнер, настроенный в параметрах получения, или главный партнер, настроенный в параметрах отправки, для соглашения AS2. |
| Получатель | Главный партнер, настроенный в параметрах получения, или гостевой партнер, настроенный в параметрах отправки, для соглашения AS2. |
| приложение логики; | Приложение логики, в котором настроены действия AS2. |
| Состояние | Состояние сообщения X12. Success (Успешно) = получено или отправлено нормальное сообщение X12, функциональное подтверждение не настроено; Success (Успешно) = получено или отправлено нормальное сообщение X12, функциональное подтверждение настроено, получено или отправлено функциональное подтверждение; Failed (Сбой) = получено или отправлено недопустимое сообщение X12; Pending (Ожидание) = получено или отправлено нормальное сообщение X12, функциональное подтверждение настроено и ожидается функциональное подтверждение. |
| Ack | Состояние функционального подтверждения (997).  Accepted (Принято) = получено или отправлено положительное функциональное подтверждение; Rejected (Отклонено) = получено или отправлено отрицательное функциональное подтверждение; Pending (Ожидание) = ожидается функциональное подтверждение, но еще не получено; Pending (Ожидание) = функциональное подтверждение создано, но не удалось отправить его партнеру. |
| Направление | Направление сообщения X12. |
| Идентификатор корреляции | Идентификатор для корреляции всех триггеров и действий в приложении логики. |
| Тип сообщения |  Тип сообщения EDI X12. |
| ICN | Контрольный номер обмена сообщения X12. |
| TSCN | Контрольный номер набора транзакций сообщения X12. |
| Timestamp | Время обработки сообщения действием X12. |
| | |

4. Если выбрать строку в списке сообщений AS2 или X12, то откроется страница поиска по журналам.  На странице поиска журналов указаны все действия с одинаковым **идентификатором запуска**
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

* На странице поиска в журнале щелкните **Избранное**, чтобы просмотреть сохраненные запросы.  Выбрав один из них, вы сможете получить результаты этого запроса. ![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Дальнейшие действия
[Настраиваемая схема отслеживания](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Схема отслеживания AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Схема отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Дополнительные сведения о Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO4-->


