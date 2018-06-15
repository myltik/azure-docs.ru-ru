---
title: Отслеживание транзакций B2B и настройка ведения журнала с помощью Azure Logic Apps | Документы Майкрософт
description: Мониторинг сообщений AS2, X 12 и EDIFACT, запуск ведения журнала диагностики для учетной записи интеграции
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
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 399c7b91949a854f3a152e9a3788d9163c565934
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299323"
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Мониторинг и настройка ведения журнала диагностики для взаимодействия B2B в учетных записях интеграции

После настройки взаимодействия B2B между двумя выполняющимися бизнес-процессами или приложениями с помощью учетной записи интеграции эти сущности могут обмениваться сообщениями друг с другом. Чтобы убедиться в том, что это взаимодействие осуществляется должным образом, можно настроить мониторинг сообщений AS2, X12 и EDIFACT, а также ведение журналов диагностики для учетной записи интеграции с помощью службы [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Эта служба в отслеживает облачную и локальную среды, помогая обеспечить их доступность и производительность, а также собирает сведения о времени выполнения и событиях для отладки. Также можно [использовать полученные диагностические данные в других службах](#extend-diagnostic-data), таких как служба хранилища Azure и концентраторы событий Azure.

## <a name="requirements"></a>Требования

* Приложение логики, настроенное на ведение журнала диагностики. Узнайте о том, [как настроить ведение журнала для этого приложения логики](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Кроме этого, у вас должна быть рабочая область в Log Analytics. При настройке ведения журнала для своей учетной записи интеграции следует использовать одну и ту же рабочую область Log Analytics. См. дополнительные сведения о [создании рабочей области Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

* Учетная запись интеграции, связанная с приложением логики. Узнайте, [как создать учетную запись интеграции и связать ее с приложениями логики](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Включение ведения журнала диагностики для учетной записи интеграции

Вы можете включить ведение журнала либо непосредственно в учетной записи интеграции, либо в [службе Azure Monitor](#azure-monitor-service). Служба Azure Monitor предоставляет основные функции мониторинга с помощью данных на уровне инфраструктуры. Узнайте подробнее о службе [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Включение ведения журнала диагностики в учетной записи интеграции

1. На [портале Azure](https://portal.azure.com) найдите и выберите свою учетную запись интеграции. В разделе **Мониторинг** выберите **Журналы диагностики**, как показано ниже.

   ![Найдите и выберите учетную запись интеграции, а затем — "Журналы диагностики"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. После выбора учетной записи интеграции автоматически выбираются следующие значения. Если эти значения верны, выберите **Включить диагностику**. В противном случае выберите необходимые значения.

   1. В поле **Подписка** выберите подписку Azure, которую хотите использовать для учетной записи интеграции.
   2. В поле **Группа ресурсов** выберите группу ресурсов, которую хотите использовать для учетной записи интеграции.
   3. В раскрывающемся списке **Тип ресурса** выберите **Учетные записи интеграции**. 
   4. В раскрывающемся списке **Ресурс** выберите учетную запись интеграции. 
   5. Выберите **Включить диагностику**.

   ![Настройка системы диагностики для учетной записи интеграции](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. В разделе **Параметры диагностики** > **Состояние** выберите **Вкл**.

   ![Включение системы диагностики Microsoft Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Теперь выберите рабочую область Log Analytics и данные, которые необходимо использовать для ведения журнала, как показано ниже:

   1. Установите флажок **Отправить в Log Analytics**. 
   2. В разделе **Log Analytics** выберите **Настройка**. 
   3. В разделе **Рабочие области OMS** выберите рабочую область Log Analytics для ведения журнала.
   4. В разделе **Журнал** выберите категорию **IntegrationAccountTrackingEvents**.
   5. Нажмите **Сохранить**.

   ![Настройка Log Analytics для отправки диагностических данных в журнал](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Теперь [настройте отслеживание сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Включение ведения журнала диагностики в службе Azure Monitor

1. На [портале Azure](https://portal.azure.com) в главном меню Azure выберите **Мониторинг**, **Журналы диагностики**. Затем выберите учетную запись интеграции, как показано ниже:

   ![Выберите "Мониторинг", "Журналы диагностики", выберите свою учетную запись интеграции](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. После выбора учетной записи интеграции автоматически выбираются следующие значения. Если эти значения верны, выберите **Включить диагностику**. В противном случае выберите необходимые значения.

   1. В поле **Подписка** выберите подписку Azure, которую хотите использовать для учетной записи интеграции.
   2. В поле **Группа ресурсов** выберите группу ресурсов, которую хотите использовать для учетной записи интеграции.
   3. В раскрывающемся списке **Тип ресурса** выберите **Учетные записи интеграции**.
   4. В раскрывающемся списке **Ресурс** выберите учетную запись интеграции.
   5. Выберите **Включить диагностику**.

   ![Настройка системы диагностики для учетной записи интеграции](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. В разделе **Параметры диагностики** выберите **Вкл**.

   ![Включение системы диагностики Microsoft Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Теперь выберите рабочую область Log Analytics и категорию событий, которые необходимо использовать для ведения журнала, как показано ниже:

   1. Установите флажок **Отправить в Log Analytics**. 
   2. В разделе **Log Analytics** выберите **Настройка**. 
   3. В разделе **Рабочие области OMS** выберите рабочую область Log Analytics для ведения журнала.
   4. В разделе **Журнал** выберите категорию **IntegrationAccountTrackingEvents**.
   5. Когда все будет готово, нажмите **Сохранить**.

   ![Настройка Log Analytics для отправки диагностических данных в журнал](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Теперь [настройте отслеживание сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Расширение возможностей использования диагностических данных в других службах

Помимо Azure Log Analytics, можно расширить возможности использования диагностических данных приложения логики в других службах Azure, например: 

* [Архивация журналов диагностики Azure в службе хранилища Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Потоковая передача журналов диагностики в концентраторы событий Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

После этого можно организовать мониторинг в режиме реального времени с помощью данных телеметрии и аналитики из других служб, таких как [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) и [Power BI](../log-analytics/log-analytics-powerbi.md). Например: 

* [Потоковая передача данных из концентраторов событий в Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Анализ потоковой передачи данных с помощью Stream Analytics и создание панели мониторинга в Power BI для анализа данных в режиме реального времени](../stream-analytics/stream-analytics-power-bi-dashboard.md)

В зависимости от вариантов использования, которые нужно настроить, сначала необходимо [создать учетную запись хранения Azure](../storage/common/storage-create-storage-account.md) или [создать концентратор событий Azure](../event-hubs/event-hubs-create.md). Затем выберите варианты отправки данных диагностики:

![Отправка данных в учетную запись хранения Azure или в концентратор событий](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Сроки хранения применяются только в том случае, если вы решили использовать учетную запись хранилища.

## <a name="supported-tracking-schemas"></a>Поддерживаемые схемы отслеживания

Azure поддерживает приведенные ниже типы схем отслеживания. Все схемы, за исключением настраиваемой, являются фиксированными.

* [Схема отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схема отслеживания X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемая схема отслеживания](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дополнительная информация

* [Отслеживание сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Отслеживание сообщений B2B в OMS")
* [Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")

