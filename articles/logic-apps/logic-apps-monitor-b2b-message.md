---
title: "Отслеживание сообщений в транзакциях B2B с помощью Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как с помощью службы Logic Apps в учетной записи интеграции отслеживать сообщения при взаимодействии &quot;бизнес — бизнес&quot; (B2B) между процессами и приложениями."
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
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 52fa2ceb13e6c14d0dd4ba72a76328a2a9356bab
ms.openlocfilehash: 79acdf2c29c3520fc38adf6270bcae68e87aac18


---
# <a name="monitor-b2b-messages"></a>Мониторинг сообщений B2B

Взаимодействие B2B включает обмен сообщениями между двумя запущенными бизнес-процессами или приложениями. Связь определяет соглашение между бизнес-процессами. Когда связь установлена, можно настроить отслеживание сообщений, чтобы проверить состояние взаимодействия.
Для получения более подробных сведений и рекомендаций по отладке можно настроить в учетной записи интеграции систему диагностики.

Отслеживание сообщений доступно для следующих протоколов B2B: AS2, X12 и EDIFACT. 

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Учетная запись интеграции. Вы можете создать [учетную запись интеграции](logic-apps-enterprise-integration-create-integration-account.md).
* Приложение логики. Вы можете создать [приложение логики](logic-apps-create-a-logic-app.md) и [включить ведение журнала](logic-apps-monitor-your-logic-apps.md).

## <a name="enable-logging-for-an-integration-account"></a>Включение ведения журналов для учетной записи интеграции

Вы можете включить ведение журнала для учетной записи интеграции с помощью **портала Azure** или службы **Azure Monitor**.

### <a name="enable-logging-with-azure-portal"></a>Включение ведения журнала с помощью портала Azure

1. Выберите учетную запись интеграции, а затем — **Журналы диагностики**.

    ![Выбор учетной записи интеграции](media/logic-apps-monitor-b2b-message/pic5.png)

2. Выберите **подписку** и **группу ресурсов**. В списке **Тип ресурса** выберите **Учетные записи интеграции**. В списке **Ресурс** выберите учетную запись интеграции. Щелкните **Turn on Diagnostics** (Включить диагностику), чтобы включить систему диагностики для выбранной учетной записи интеграции.

    ![Настройка системы диагностики для учетной записи интеграции](media/logic-apps-monitor-b2b-message/pic2.png)

3. Выберите состояние **Вкл.**

    ![Выбор состояния системы диагностики](media/logic-apps-monitor-b2b-message/pic3.png)

4. Установите флажок **Send to Log Analytics** (Отправить в Log Analytics) и настройте Log Analytics для передачи данных.

    ![Отправка данных диагностики в журнал](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>Включение ведения журналов с помощью средства мониторинга

0. Выберите **Монитор** и щелкните **Журналы диагностики**.

    ![Выбор "Монитор" > "Журналы диагностики"](media/logic-apps-monitor-b2b-message/pic1.png)

0. Выберите **подписку** и **группу ресурсов**. В списке **Тип ресурса** выберите **Учетные записи интеграции**. В списке **Ресурс** выберите учетную запись интеграции. Щелкните **Turn on Diagnostics** (Включить диагностику), чтобы включить систему диагностики для выбранной учетной записи интеграции.

    ![Настройка системы диагностики для учетной записи интеграции](media/logic-apps-monitor-b2b-message/pic2.png)

0. Выберите состояние **Вкл.**

    ![Выбор состояния системы диагностики](media/logic-apps-monitor-b2b-message/pic3.png) 

0. Установите флажок **Send to Log Analytics** (Отправить в Log Analytics) и настройте **Log Analytics** для передачи данных.

    ![Отправка данных диагностики в журнал](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>Расширение решений

Помимо **Log Analytics** для учетной записи интеграции и службы [Logic Apps](./logic-apps-monitor-your-logic-apps.md) можно настроить концентратор событий или учетную запись хранения.

![Параметры системы диагностики Azure](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

Вы можете использовать эти данные телеметрии из концентратора событий или хранилища в других службах, например [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) и [Power BI](https://powerbi.com), чтобы отслеживать рабочие процессы интеграции в режиме реального времени.

## <a name="supported-tracking-schema"></a>Поддерживаемая схема отслеживания

Корпорация Майкрософт поддерживает приведенные ниже типы схем отслеживания. Все схемы, за исключением настраиваемой, являются фиксированными.

* [Настраиваемая схема отслеживания](logic-apps-track-integration-account-custom-tracking-schema.md)
* [Схема отслеживания AS2](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схема отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

[Отслеживание сообщений B2B на портале OMS](logic-apps-track-b2b-messages-omsportal.md "Отслеживание сообщений B2B")

[Обзор пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")




<!--HONumber=Jan17_HO5-->


