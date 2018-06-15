---
title: Запрос сообщений B2B в Log Analytics — Azure Logic Apps | Документация Майкрософт
description: Создание запросов на отслеживание сообщений AS2, X12 и EDIFACT в Log Analytics
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 520a1212eaccc48f8b8b423f7dede9c16409220b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300333"
---
# <a name="query-for-as2-x12-and-edifact-messages-in-log-analytics"></a>Запрос сообщений AS2, X12 и EDIFACT в Log Analytics

Чтобы найти сообщения AS2, X12 или EDIFACT, которые отслеживаются с помощью [Azure Log Analytics](../log-analytics/log-analytics-overview.md), можно создавать запросы, применяющие фильтры по требуемым критериям. Например, можно найти сообщения с определенным контрольным номером обмена.

## <a name="requirements"></a>Требования

* Приложение логики, настроенное на ведение журнала диагностики. Узнайте подробнее о [создании приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) и [настройке ведения журнала для такого приложения логики](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Учетная запись интеграции, настроенная для мониторинга и ведения журнала. Узнайте подробнее о [создании учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) и [настройке мониторинга и ведения журнала для этой учетной записи](../logic-apps/logic-apps-monitor-b2b-message.md).

* Если это еще не сделано, [опубликуйте данные диагностики в службе Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) и [настройте отслеживание сообщений в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Кроме этого, у вас должна быть рабочая область в Log Analytics. Для отслеживания взаимодействия B2B используйте ту же рабочую область Log Analytics. 
>  
> См. дополнительные сведения о [создании рабочей области Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Создание запросов сообщений с фильтрами в службе Log Analytics

В этом примере показано, как найти сообщение по контрольному номеру обмена.

> [!TIP] 
> Если известно название рабочей области в Log Analytics, перейдите на домашнюю страницу этой рабочей области (`https://{your-workspace-name}.portal.mms.microsoft.com`) и начинайте с шага 4. В противном случае начните с шага 1.

1. На [портале Azure](https://portal.azure.com) выберите **Все службы**. Введите в поле поиска "log analytics" и выберите **Log Analytics**, как показано ниже.

   ![Поиск Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. В разделе **Log Analytics** найдите и выберите рабочую область Log Analytics.

   ![Выбор рабочей области Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. В разделе **Управление** выберите **Портал OMS**.

   ![Выбор портала OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. На домашней странице выберите **Поиск по журналам**.

   ![На домашней странице выберите "Поиск по журналам".](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -или-

   ![В меню выберите "Поиск по журналам".](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. В поле поиска введите поле, которое требуется найти, и нажмите клавишу **ВВОД**. При вводе Log Analytics предлагает возможные совпадения и операции, которые можно использовать. Узнайте подробнее о [способах поиска данных в Log Analytics](../log-analytics/log-analytics-log-searches.md).

   В этом примере выполняется поиск событий с параметром **Type=AzureDiagnostics**.

   ![Начните вводить строку запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. На панели слева выберите период времени, данные за который вы хотите просмотреть. Чтобы добавить фильтр к запросу, нажмите **+Добавить**.

   ![Добавление фильтра к запросу](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. В разделе **Добавить фильтры** введите имя фильтра, чтобы найти нужный фильтр. Выберите фильтр и нажмите **+Добавить**.

   Чтобы найти контрольный номер обмена, в этом примере выполняется поиск по слову "interchange", а в качестве фильтра выбирается параметр **event_record_messageProperties_interchangeControlNumber_s**.

   ![Выбор фильтра](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. На панели слева выберите значение фильтра, которое требуется использовать, а затем нажмите **Применить**.

   В этом примере выбирается контрольный номер обмена для требуемых сообщений.

   ![Выбор значения фильтра](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Теперь вернитесь к запросу, который вы создаете. Запрос был обновлен с учетом выбранных фильтра событий и значения. Предыдущие результаты теперь также отфильтрованы.

    ![Возврат к запросу с отфильтрованными результатами](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Сохранение запроса для использования в будущем

1. В запросе на странице **Поиск по журналам** нажмите **Сохранить**. Присвойте запросу имя, выберите категорию и нажмите **Сохранить**.

   ![Присвойте запросу имя и категорию](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Чтобы просмотреть запрос, выберите **Избранное**.

   ![Выберите "Избранное"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. В разделе **Сохраненные условия поиска** выберите запрос, чтобы просмотреть его результаты. Чтобы обновить запрос и найти другие результаты, измените запрос.

   ![Выбор запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Поиск и запуск сохраненных запросов в Log Analytics

1. Откройте домашнюю страницу рабочей области Log Analytics (`https://{your-workspace-name}.portal.mms.microsoft.com`) и выберите **Поиск по журналам**.

   ![На домашней странице Log Analytics выберите "Поиск по журналам"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -или-

   ![В меню выберите "Поиск по журналам".](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. На домашней странице **Поиск по журналам** выберите **Избранное**.

   ![Выберите "Избранное"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. В разделе **Сохраненные условия поиска** выберите запрос, чтобы просмотреть его результаты. Чтобы обновить запрос и найти другие результаты, измените запрос.

   ![Выбор запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Дополнительная информация

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схемы отслеживания X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)