---
title: "Запрос сообщений B2B с помощью Operations Management Suite в Azure Logic Apps | Документы Майкрософт"
description: "Отслеживание сообщений AS2, X12 и EDIFACT на портале Operations Management Suite с помощью запросов"
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
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 69e8735417bc006296b8a6e5168d9fbeff052247
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Запрос сообщений AS2, X 12 и EDIFACT в Microsoft Operations Management Suite (OMS)

Чтобы найти сообщения AS2, X12 или EDIFACT, которые отслеживаются с помощью [Azure Log Analytics](../log-analytics/log-analytics-overview.md) в [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), можно создавать запросы, применяющие фильтры к действиям на основе заданных критериев. Например, можно найти сообщения с определенным контрольным номером обмена.

## <a name="requirements"></a>Требования

* Приложение логики, настроенное на ведение журнала диагностики. Узнайте подробнее о [создании приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) и [настройке ведения журнала для такого приложения логики](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Учетная запись интеграции, настроенная для мониторинга и ведения журнала. Узнайте подробнее о [создании учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) и [настройке мониторинга и ведения журнала для этой учетной записи](../logic-apps/logic-apps-monitor-b2b-message.md).

* Если это еще не сделано, [опубликуйте диагностические данные в службе Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) и [настройте отслеживание сообщений в OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Помимо соблюдения всех указанных выше требований, у вас должна иметься рабочая область в [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Для отслеживания взаимодействия B2B в OMS следует использовать одну и ту же рабочую область OMS. 
>  
> Если у вас нет рабочей области OMS, узнайте о том, [как ее создать](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Создание запросов сообщений с фильтрами на портале Operations Management Suite

В этом примере показано, как найти сообщение по контрольному номеру обмена.

> [!TIP] 
> Если известно имя рабочей области OMS, перейдите на домашнюю страницу рабочей области (`https://{your-workspace-name}.portal.mms.microsoft.com`) и перейдите к шагу 4. В противном случае начните с шага 1.

1. На [портале Azure](https://portal.azure.com) щелкните **Больше служб**. Введите в поле поиска "log analytics" и выберите **Log Analytics**, как показано ниже.

   ![Поиск Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. В разделе **Log Analytics** найдите и выберите рабочую область OMS.

   ![Выбор рабочей области OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. В разделе **Управление** выберите **Портал OMS**.

   ![Выбор портала OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. На домашней странице OMS выберите **Поиск по журналам**.

   ![На домашней странице OMS выберите "Поиск по журналам"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -или-

   ![В меню OMS выберите "Поиск по журналам"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. В поле поиска введите поле, которое требуется найти, и нажмите клавишу **ВВОД**. При вводе OMS предлагает возможные совпадения и операции, которые можно использовать. Узнайте подробнее о [способах поиска данных в Log Analytics](../log-analytics/log-analytics-log-searches.md).

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

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Поиск и запуск сохраненных запросов на портале Operations Management Suite

1. Откройте домашнюю страницу рабочей области OMS (`https://{your-workspace-name}.portal.mms.microsoft.com`) и выберите **Поиск по журналам**.

   ![На домашней странице OMS выберите "Поиск по журналам"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -или-

   ![В меню OMS выберите "Поиск по журналам"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. На домашней странице **Поиск по журналам** выберите **Избранное**.

   ![Выберите "Избранное"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. В разделе **Сохраненные условия поиска** выберите запрос, чтобы просмотреть его результаты. Чтобы обновить запрос и найти другие результаты, измените запрос.

   ![Выбор запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Дополнительная информация

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схемы отслеживания X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)