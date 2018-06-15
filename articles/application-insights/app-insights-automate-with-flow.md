---
title: Автоматизация процессов Azure Application Insights с помощью Microsoft Flow
description: Узнайте, как можно использовать Microsoft Flow для быстрой автоматизации повторяющихся процессов с помощью соединителя Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: c92b50ee78c100a531a2973181251444b3810967
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293780"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Автоматизация процессов Azure Application Insights с помощью соединителя для Microsoft Flow

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на их основе собственные рабочие процессы? Соединитель Application Insights (предварительная версия) для Microsoft Flow — это то, что вам нужно.

Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. После создания последовательности с помощью действия Application Insights будет автоматически запущен запрос Application Insights Analytics. 

Вы также можете добавить дополнительные действия. Служба Flow предоставляет сотни действий. Например, вы можете использовать Microsoft Flow, чтобы автоматически отправить уведомление по электронной почте или создать ошибку в Visual Studio Team Services. Вы также можете воспользоваться одним из нескольких [шаблонов](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights), доступных для соединителя для Microsoft Flow. С помощью этих шаблонов можно ускорить процесс создания последовательности. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Создание последовательности для Application Insights

В этом руководстве вы узнаете, как создать последовательность, использующую алгоритм автоматической кластеризации Analytics, для группирования атрибутов данных для веб-приложения. Отправка результатов по электронной почте — это лишь один из примеров того, как можно совместно использовать Microsoft Flow и Application Insights Analytics. 

### <a name="step-1-create-a-flow"></a>Шаг 1. Создание последовательности
1. Войдите в службу [Microsoft Flow](http://flow.microsoft.com) и выберите **Мои последовательности**.
2. Щелкните **Create a flow from blank** (Создать последовательность с нуля).

### <a name="step-2-create-a-trigger-for-your-flow"></a>Шаг 2. Создание триггера для последовательности
1. Выберите **Расписание**, а затем **Расписание — повторение**.
2. В поле **Частота** выберите **День**, а в поле **Интервал** введите **1**.

    ![Диалоговое окно триггера Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Шаг 3. Добавление действия Application Insights
1. Выберите поле **+Новый шаг**, а затем щелкните **Добавить действие**.
2. Выполните поиск по запросу **Azure Application Insights**.
3. Выберите **Azure Application Insights – Visualize Analytics query Preview** (Azure Application Insights — визуализация запроса Analytics [предварительная версия]).

    ![Окно запуска запроса Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано ниже.

![Идентификатор приложения на портале Azure](./media/app-insights-automate-with-flow/appid.png) 

- Укажите имя подключения, а также идентификатор приложения и ключ API.

    ![Окно подключения Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Шаг 5. Указание запроса Analytics и типа диаграммы
В этом примере выбираются невыполненные запросы за последний день. Они сопоставляются с исключениями, которые возникли в рамках операции. Analytics сопоставляет их на основе идентификатора operation_Id. Затем запрос разделяет результаты с помощью алгоритма автоматической кластеризации. 

При создании собственных запросов убедитесь, что они работают должным образом в Analytics, прежде чем добавить их в последовательность.

- Добавьте следующий запрос Analytics и выберите тип диаграммы "Таблица HTML". 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Окно настройки запроса Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Шаг 6. Настройка последовательности для отправки электронной почты

1. Выберите поле **+Новый шаг**, а затем щелкните **Добавить действие**.
2. Выполните поиск по запросу **Office 365 Outlook**.
3. Щелкните **Office 365 Outlook – Send an email** (Office 365 Outlook — отправка сообщения электронной почты).

    ![Окно выбора Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. В окне **Отправка сообщения электронной почты** выполните следующие действия:

   a. Введите адрес электронной почты получателя.

   Б. Введите тему сообщения.

   c. Щелкните в любом месте в поле **Текст**, затем в открывшемся справа меню динамического содержимого выберите **Текст**.

   d. Щелкните **Показать дополнительные параметры**.

    ![Конфигурация Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. В меню динамического содержимого выполните следующее.

    a. Выберите **Имя вложения**.

    Б. Выберите **Содержимое вложения**.
    
    c. В поле **Является HTML** выберите значение **Да**.

    ![Экран настройки сообщения электронной почты Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Шаг 7. Сохранение и тестирование последовательности
- В поле **Имя последовательности** добавьте имя последовательности и нажмите кнопку **Создать последовательность**.

    ![Окно создания последовательности](./media/app-insights-automate-with-flow/flow8.png)

Вы можете подождать, пока триггер запустит действие, или немедленно запустить последовательность, [запустив триггер по запросу](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

При выполнении последовательности получатели, указанные в списке рассылки, получат сообщение электронной почты, которое выглядит следующим образом:

![Пример электронного сообщения](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о создании [запросов Analytics](app-insights-analytics-using.md).
- Дополнительные сведения о [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





