---
title: "Отслеживание сообщений B2B на портале Operations Management Suite в Azure | Документация Майкрософт"
description: "Как отслеживать сообщения B2B на портале Operations Management Suite"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 4d68fde29e85fcf9aa623b4d798262bcd486a8bb
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Отслеживание сообщений B2B на портале Operations Management Suite
Взаимодействие B2B включает обмен сообщениями между двумя запущенными бизнес-процессами или приложениями. Следующие функции отслеживания на веб-портале Operations Management Suite позволяют подтвердить, что сообщения обработаны правильно:

* число и состояние сообщений;
* состояние подтверждений;
* сопоставление сообщений с подтверждениями;
* подробное описание ошибок при сбоях;
* возможности поиска.

## <a name="prerequisites"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Учетная запись интеграции. Вы можете создать [учетную запись интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) и настроить ведение журнала. Настройка ведения журнала описана в статье [Мониторинг сообщений B2B](logic-apps-monitor-b2b-message.md).
* Приложение логики. Вы можете создать [приложение логики](../logic-apps/logic-apps-create-a-logic-app.md) и настроить ведение журнала. Настройка ведения журнала описана в разделе [Система диагностики и оповещения Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Добавление решения B2B Logic Apps на портал Operations Management Suite

1. На портале Azure выберите **Больше служб**, введите в поле поиска "log analytics" и выберите **Log Analytics**.   
![Поиск Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Выберите запись **Log Analytics**.  
![Выбор записи Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Выберите **Портал OMS**. Откроется стартовая страница портала Operations Management Suite.   
![Просмотр портала Operations Management Suite](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Выберите **Коллекция решений**.    
![Выбор коллекции решений](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Выберите **Logic Apps B2B**.     
![Выбор Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Щелкните **Добавить**, чтобы добавить сообщения **Logic Apps B2B** на домашнюю страницу.  
![Выбор кнопки "Добавить"](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. На домашней странице появится элемент **Сообщения Logic Apps B2B**.   
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Отслеживание данных на портале Operations Management Suite

1. После обработки сообщений отображается число обновленных сообщений.   
![Обновленные сообщения](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Выберите **Сообщения Logic Apps B2B** на домашней странице, чтобы увидеть состояние сообщений AS2 и X12.  Данные предоставляются за один день.
![Выбор сообщений B2B для приложений логики](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Выберите, с каким статусом сообщения AS2 или X12 должны отображаться в списке сообщений. На следующем снимке экрана показано состояние сообщения AS2. Свойства состояния для сообщений AS2 и X12 описаны далее в разделе "Описания свойств в списке сообщений".  
![Выбор состояния сообщений AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)
4. Выберите строку в списке сообщений AS2 или X12, чтобы открыть поиск по журналам.  Функция поиска по журналам возвращает все действия с одинаковым идентификатором выполнения.
![Выбор состояния сообщений](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>Описания свойств в списке сообщений

#### <a name="as2-message-list-property-descriptions"></a>Описания свойств в списке сообщений AS2

| Свойство | Описание |
| --- | --- |
| Отправитель | Гостевой партнер, настроенный в параметрах получения, или главный партнер, настроенный в параметрах отправки, для соглашения AS2. |
| Получатель | Главный партнер, настроенный в параметрах получения, или гостевой партнер, настроенный в параметрах отправки, для соглашения AS2. |
| приложение логики; | Приложение логики, в котором настроены действия AS2. |
| Состояние | Состояние сообщения AS2. <br>Success: получено или отправлено корректное сообщение AS2, MDN не настроено. <br>Success: получено или отправлено корректное сообщение AS2, MDN настроено и получено либо отправлено. <br>Failed: получено некорректное сообщение AS2, MDN не настроено. <br>Pending: получено или отправлено корректное сообщение AS2, MDN настроено и ожидается. |
| Ack | Состояние сообщения MDN. <br>Accepted: получено или отправлено положительное MDN. <br>Pending: ожидается получение или отправка MDN. <br>Rejected: получено или отправлено отрицательное MDN. <br>Not Required: в соглашении не настроено MDN. |
| Направление | Направление сообщения AS2. |
| Идентификатор корреляции | Идентификатор для корреляции всех триггеров и действий в приложении логики. |
| Идентификатор сообщения |  Идентификатор сообщения AS2, полученный из заголовков сообщения AS2. |
| Timestamp | Время обработки сообщения действием AS2. |

#### <a name="x12-message-list-property-descriptions"></a>Описания свойств в списке сообщений X12

| Свойство | Описание |
| --- | --- |
| Отправитель | Гостевой партнер, настроенный в параметрах получения, или главный партнер, настроенный в параметрах отправки, для соглашения AS2. |
| Получатель | Главный партнер, настроенный в параметрах получения, или гостевой партнер, настроенный в параметрах отправки, для соглашения AS2. |
| приложение логики; | Приложение логики, в котором настроены действия AS2. |
| Состояние | Состояние сообщения X12. <br>Success: получено или отправлено корректное сообщение X12, функциональное подтверждение не настроено. <br>Success: получено или отправлено корректное сообщение X12, функциональное подтверждение настроено и либо получено, либо отправлено. <br>Failed: получено или отправлено некорректное сообщение X12. <br>Pending: получено или отправлено корректное сообщение X12, функциональное подтверждение настроено и ожидается его получение. |
| Ack | Состояние функционального подтверждения (997). <br>Accepted: получено или отправлено положительное функциональное подтверждение. <br>Rejected: получено или отправлено отрицательное функциональное подтверждение. <br>Pending: ожидается функциональное подтверждение, но оно еще не получено. <br>Pending: функциональное подтверждение создано, но его не удалось отправить в партнер. <br>Not Required: функциональное подтверждение не настроено. |
| Направление | Направление сообщения X12. |
| Идентификатор корреляции | Идентификатор для корреляции всех триггеров и действий в приложении логики. |
| Тип сообщения |  Тип сообщения EDI X12. |
| ICN | Контрольный номер обмена для сообщения X12. |
| TSCN | Контрольный номер набора транзакций для сообщения X12. |
| Timestamp | Время обработки сообщения действием X12. |

## <a name="queries-in-the-operations-management-suite-portal"></a>Запросы на портале Operations Management Suite

На странице поиска можно создать запрос. Результаты поиска можно фильтровать с помощью элементов управления аспектами.

### <a name="create-a-query"></a>Создание запроса

1. На странице поиска в журнале создайте запрос и щелкните **Сохранить**. Откроется диалоговое окно **Сохранение условий поиска**. Создание запросов описано в статье [Отслеживание сообщений B2B на портале Operations Management Suite с помощью запроса](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md).
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. В окне **Сохранение условий поиска** введите **имя** и **категорию**, а затем выберите **Сохранить**.   
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Чтобы просмотреть запрос, щелкните **Избранное**.    
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>Использование сохраненного запроса

* На странице поиска в журнале щелкните **Избранное**, чтобы просмотреть сохраненные запросы.  Чтобы просмотреть результаты запроса, выберите нужный запрос.
![Выбор домашней страницы](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Дальнейшие действия
[Настраиваемая схема отслеживания](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Схема отслеживания AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Схема отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Дополнительные сведения о Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

