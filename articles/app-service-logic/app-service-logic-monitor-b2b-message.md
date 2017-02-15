---
title: "Мониторинг сообщений B2B | Документация Майкрософт"
description: "Мониторинг учетной записи интеграции"
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
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>Мониторинг сообщений B2B
Взаимодействие B2B включает обмен сообщениями между двумя запущенными бизнес-процессами или приложениями. Связь определяет соглашение между бизнес-процессами. После установки взаимодействия необходимо настроить наблюдение за состоянием взаимодействия.  Отслеживание сообщений реализовано для протоколов B2B: AS2, X12 и EDIFACT.  Вы можете настроить в учетной записи интеграции использование диагностики для получения более подробных сведений и рекомендаций по отладке.

## <a name="prerequisites"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Учетная запись интеграции. Вы можете создать [учетную запись интеграции](app-service-logic-enterprise-integration-create-integration-account.md).
* Приложение логики. Вы можете создать [приложение логики](app-service-logic-create-a-logic-app.md) и включить ведение журнала. Соответствующие действия см. [здесь](app-service-logic-monitor-your-logic-apps.md).

## <a name="enable-logging-for-an-integration-account"></a>Включение ведения журналов для учетной записи интеграции
Вы можете включить ведение журналов для учетной записи интеграции с помощью **портала Azure** или **средства мониторинга**.

### <a name="enable-logging-with-azure-portal"></a>Включение ведения журнала с помощью портала Azure

1. Выберите **учетную запись интеграции** и **журналы диагностики** 
![выбор учетной записи интеграции](./media/app-service-logic-monitor-integration-account/Pic5.png).  

2. Выберите **подписку**, **группу ресурсов** и **учетную запись интеграции** в качестве типа ресурса. Затем в раскрывающемся списке ресурсов выберите **Учетная запись интеграции** для включения диагностики.  Щелкните **Turn on Diagnostics** (Включить диагностику), чтобы включить диагностику для выбранной учетной записи интеграции.               
![Выбор учетной записи интеграции](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. Выберите состояние **Вкл.**       
![Включение диагностики](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Установите флажок **Send to Log Analytics** (Отправить в Log Analytics) и настройте Log Analytics для передачи данных.               
![Включение диагностики](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>Включение ведения журналов с помощью средства мониторинга

1. Выберите **Мониторинг** и щелкните **Журналы диагностики** .  
![Выбор мониторинга](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. Выберите **подписку**, **группу ресурсов** и **учетную запись интеграции** в качестве типа ресурса. Затем в раскрывающемся списке ресурсов выберите **Учетная запись интеграции** для включения диагностики.  Щелкните **Turn on Diagnostics** (Включить диагностику), чтобы включить диагностику для выбранной учетной записи интеграции.               
![Выбор учетной записи интеграции](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. Выберите состояние **Вкл.**       
![Включение диагностики](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Установите флажок **Send to Log Analytics** (Отправить в Log Analytics) и настройте Log Analytics для передачи данных. ![Включение диагностики](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>Расширение решений
Помимо **Log Analytics** для учетной записи интеграции и [приложений логики](./app-service-logic-monitor-your-logic-apps.md) можно настроить концентратор событий или учетную запись хранения.         
![Параметры системы диагностики Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

Вы можете использовать эти данные телеметрии из концентратора событий или хранилища в других службах, например [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) и [Power BI](https://powerbi.com), чтобы отслеживать рабочие процессы интеграции в режиме реального времени.

## <a name="supported-tracking-schema"></a>Поддерживаемая схема отслеживания
Ниже приведены поддерживаемые типы схем отслеживания.  Все схемы, за исключением пользовательской, являются фиксированными.

* [Настраиваемая схема отслеживания](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [Схема отслеживания AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Схема отслеживания X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>Дальнейшие действия
[Отслеживание сообщений B2B на портале OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


