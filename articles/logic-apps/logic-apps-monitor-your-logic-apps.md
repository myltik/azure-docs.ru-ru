---
title: Проверка состояния, настройка ведения журнала и получение оповещений в Azure Logic Apps | Документы Майкрософт
description: Мониторинг состояния и производительности приложений логики, запись ведения журнала диагностики и настройка оповещений
author: jeffhollan
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: c1d5bc55b132b449ebc2964ef95016a6a4780c19
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Мониторинг состояния, настройка ведения журнала диагностики и включение предупреждений для Azure Logic Apps

После [создания и запуска приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) можно проверить его журнал запусков, журнал триггеров, состояние и производительность. Для мониторинга событий в режиме реального времени и отладки можно настроить [ведение журнала диагностики](#azure-diagnostics) для приложения логики. Это позволит [выполнять поиск и просматривать события](#find-events), такие как события триггера, события выполнения и события действия. Также можно [использовать полученные диагностические данные в других службах](#extend-diagnostic-data), таких как служба хранилища Azure и концентраторы событий Azure. 

Чтобы получать уведомления о сбоях или других возможных проблемах, настройте [оповещения](#add-azure-alerts). Например, можно создать оповещение, которое обнаруживает "более пяти запусков в течение часа, которые завершились сбоем". Можно также настроить мониторинг, отслеживание и ведение журнала программными средствами с помощью [параметров и свойств событий системы диагностики Azure](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Просмотр сведений о выполнении и журнале триггеров для приложения логики

1. Чтобы найти приложение логики на [портале Azure](https://portal.azure.com), в главном меню Azure выберите **Все службы**. В поле поиска введите "logic apps", а затем выберите **Logic Apps**.

   ![Поиск приложения логики](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   На портале Azure будут перечислены все приложения логики, которые связаны с подпиской Azure. 

2. Выберите приложение логики, а затем нажмите **Обзор**.

   На портале Azure отобразятся журнал запусков и журнал триггеров для приложения логики. Например: 

   ![Журнал запусков и журнал триггеров для приложения логики](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Журнал запусков** содержит сведения о всех запусках приложения логики. 
   * **Журнал триггеров** содержит все действия триггеров приложения логики.

   Описание состояний см. в разделе [Устранение неполадок в работе приложения логики](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Если вы не нашли ожидаемые данные, нажмите кнопку **Обновить**.

3. Чтобы просмотреть этапы выполнения определенного запуска, выберите соответствующий запуск в разделе **Журнал запусков**. 

   В представлении монитора отобразится каждый этап данного запуска. Например: 

   ![Действия для определенного запуска](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Чтобы получить подробные сведения о запуске, нажмите **Подробности о запуске**. В этих сведениях представлены этапы, состояние, входные и выходные данные для запуска. 

   ![Выберите "Подробности о запуске"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Например, можно получить **идентификатор корреляции** запуска, которой может использоваться для [REST API для Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Чтобы просмотреть подробные сведения об определенном этапе, выберите этот этап. После этого можно просмотреть такие сведения для данного этапа, как входные данные, выходные данные и все ошибки. Например: 

   ![Сведения об этапе](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Все сведения о времени выполнения и события шифруются в службе Logic Apps. Они расшифровываются только в том случае, когда пользователь запрашивает просмотр этих данных. Доступ к этим событиям можно также контролировать с помощью [управления доступом на основе ролей Azure (RBAC)](../role-based-access-control/overview.md).

6. Чтобы получить сведения об определенном событии триггера, вернитесь к области **Обзор**. В разделе **Журнал триггеров** выберите событие триггера. Теперь можно просмотреть такие сведения, как входные и выходные данные, например:

   ![Выходные данные события триггера](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Включение ведения журнала диагностики для приложения логики

Для отладки с использованием сведений о среде выполнения и событиях можно настроить ведение журнала диагностики с помощью [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics — это служба в Azure, которая отслеживает облачные и локальные среды, помогая поддерживать уровень их доступности и производительности. 

Чтобы начать работу, вам потребуется рабочая область Log Analytics. Узнайте, [как создать рабочую область Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

1. На [портале Azure](https://portal.azure.com) найдите и выберите требуемое приложение логики. 

2. В меню в колонке приложения логики в разделе **Мониторинг** выберите **Диагностика** > **Параметры диагностики**.

   ![Выберите "Мониторинг" > "Диагностика" > "Параметры диагностики"](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. В разделе **Параметры диагностики** выберите **Вкл**.

   ![Включение журналов диагностики](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Теперь выберите рабочую область Log Analytics и категорию событий, которые необходимо использовать для ведения журнала, как показано ниже:

   1. Установите флажок **Отправить в Log Analytics**. 
   2. В разделе **Log Analytics** выберите **Настройка**. 
   3. В разделе **Рабочие области OMS** выберите рабочую область Log Analytics для ведения журнала.
   4. В разделе **Журнал** выберите категорию **WorkflowRuntime**.
   5. Выберите интервал метрики.
   6. Когда все будет готово, нажмите **Сохранить**.

   ![Выберите рабочую область Log Analytics и данные для ведения журнала](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Теперь можно выполнять поиск событий и других данных, таких как события триггера, события выполнения и события действия.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Поиск событий и данных для приложения логики

Чтобы найти и просмотреть события в приложении логики, такие как события триггера, события выполнения и события действия, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) выберите **Все службы**. Введите в поле поиска "log analytics" и выберите **Log Analytics**, как показано ниже.

   ![Выберите "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. В разделе **Log Analytics** найдите и выберите рабочую область Log Analytics. 

   ![Выбор рабочей области Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. В разделе **Управление** выберите **Портал OMS**.

   ![Выберите "Портал OMS"](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. На домашней странице выберите **Поиск по журналам**.

   ![На домашней странице выберите "Поиск по журналам".](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -или-

   ![В меню выберите "Поиск по журналам".](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. В поле поиска укажите поле, которое требуется найти, и нажмите клавишу **ВВОД**. При вводе предлагаются возможные совпадения и операции, которые можно использовать. 

   Например, чтобы найти первые 10 возникших событий, введите и выберите следующий поисковый запрос: **search Category == "WorkflowRuntime" | limit 10**.

   ![Введите строку поиска](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Узнайте подробнее о [способах поиска данных в Log Analytics](../log-analytics/log-analytics-log-searches.md).

6. На странице результатов выберите период времени, данные за который вы хотите просмотреть.
Чтобы уточнить запрос, добавив фильтр, нажмите **+Добавить**.

   ![Выберите период времени для результатов запроса](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. В разделе **Добавить фильтры** введите имя фильтра, чтобы найти нужный фильтр. Выберите фильтр и нажмите **+Добавить**.

   В этом примере используется слово "status" для поиска событий сбоя в разделе **AzureDiagnostics**.
   Здесь уже выбран фильтр для **status_s**.

   ![Выбор фильтра](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. На панели слева выберите значение фильтра, которое требуется использовать, а затем нажмите **Применить**.

   ![Выберите значение фильтра, нажмите кнопку "Применить"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Теперь вернитесь к запросу, который вы создаете. Запрос был обновлен с учетом выбранных фильтра и значения. Предыдущие результаты теперь также отфильтрованы.

   ![Возврат к запросу с отфильтрованными результатами](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Чтобы сохранить запрос для использования в будущем, нажмите **Сохранить**. Узнайте о том, [как сохранять запросы](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Расширение возможностей использования диагностических данных в других службах

Помимо Azure Log Analytics, можно расширить возможности использования диагностических данных приложения логики в других службах Azure, например: 

* [Архивация журналов диагностики Azure в службе хранилища Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Потоковая передача журналов диагностики в концентраторы событий Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

После этого можно организовать мониторинг в режиме реального времени с помощью данных телеметрии и аналитики из других служб, таких как [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) и [Power BI](../log-analytics/log-analytics-powerbi.md). Например: 

* [Потоковая передача данных из концентраторов событий в Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Анализ потоковой передачи данных с помощью Stream Analytics и создание панели мониторинга в Power BI для анализа данных в режиме реального времени](../stream-analytics/stream-analytics-power-bi-dashboard.md)

В зависимости от вариантов использования, которые нужно настроить, сначала необходимо [создать учетную запись хранения Azure](../storage/common/storage-create-storage-account.md) или [создать концентратор событий Azure](../event-hubs/event-hubs-create.md). Затем выберите варианты отправки данных диагностики:

![Отправка данных в учетную запись хранения Azure или в концентратор событий](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Сроки хранения применяются только в том случае, если вы решили использовать учетную запись хранилища.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Настройка оповещений для приложения логики

Для отслеживания определенных метрик или превышений пороговых значений для приложения логики настройте [оповещения в Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Узнайте подробнее о [метриках в Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Чтобы настроить оповещения без [Azure Log Analytics](../log-analytics/log-analytics-overview.md), выполните следующие действия. Для расширенных критериев оповещений и действий необходимо также [настроить Log Analytics](#azure-diagnostics).

1. В меню в колонке приложения логики в разделе **Мониторинг** выберите **Диагностика** > **Правила оповещений** > **Добавить оповещение**, как показано ниже:

   ![Добавление оповещения для приложения логики](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. В колонке **Добавление правила оповещения** создайте оповещение, как показано ниже:

   1. В разделе **Ресурс** выберите приложение логики, если оно еще выбрано. 
   2. Укажите имя и описание для оповещения.
   3. Выберите **Метрика** или событие, которое требуется отслеживать.
   4. Выберите **Условие**, укажите **Пороговое значение** метрики, а затем выберите **Период** для мониторинга этой метрики.
   5. Укажите, следует ли отправлять соответствующее сообщение для предупреждения. 
   6. Укажите адреса электронной почты для отправки оповещений. 
   Можно также указать URL-адрес веб-перехватчика, на который необходимо отправлять оповещение.

   Например, это правило отправляет оповещение после пяти или более сбоев выполнения в течение часа:

   ![Создание правила оповещения для метрики](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Чтобы запустить приложение логики из оповещения, в рабочий процесс можно включить [триггер запроса](../connectors/connectors-native-reqres.md), что позволяет выполнять такие задачи, как указано ниже:
> 
> * [Публикация в Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Отправка текста](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Добавление сообщения в очередь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Параметры события диагностики Azure и подробные сведения о нем

Каждое событие диагностики содержит подробные сведения о приложении логики и данном событии, например такие, как состояние, время начала, время окончания и т. д. Чтобы настроить мониторинг, отслеживание и ведение журнала, используя программные средства, эти сведения можно использовать в [REST API для Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) и [REST API для системы диагностики Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Например, у события `ActionCompleted` имеются свойства `clientTrackingId` и `trackedProperties`, которые можно использовать для отслеживания и мониторинга:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`. Если это свойство не указано, Azure автоматически создает этот идентификатор и сопоставляет события во время выполнения приложения логики, включая все вложенные рабочие процессы, вызываемые из приложения логики. Можно вручную задать этот идентификатор из триггера, передав заголовок `x-ms-client-tracking-id` с пользовательским идентификатором в запросе триггера. Можно использовать триггер запроса, триггер HTTP или триггер веб-перехватчика.

* `trackedProperties`. Для отслеживания входных и выходных данных можно добавить отслеживаемые свойства в действия в определении JSON приложения логики. Отслеживаемые свойства позволяют отслеживать входные и выходные данные только одного действия. Однако можно использовать свойства `correlation` событий, чтобы сопоставлять действия в выполнении.

  Для отслеживания одного или нескольких свойств добавьте раздел `trackedProperties` и требуемые свойства в определение действия. Например, предположим, что в телеметрии требуется отслеживать такие данные, как "Идентификатор заказа":

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>Дополнительная информация

* [Создание шаблонов для развертывания приложений логики и управления выпусками](../logic-apps/logic-apps-create-deploy-template.md)
* [Сценарии B2B с пакетом интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Мониторинг сообщений B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
