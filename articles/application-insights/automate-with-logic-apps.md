---
title: "Автоматизация процессов Azure Application Insights с помощью Logic Apps."
description: "Узнайте, как можно быстро автоматизировать повторяющиеся процессы, добавив соединитель Application Insights в приложение логики."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: e17d8076a00cab2cf608fe1a690e4a780a69d56f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Автоматизация процессов Application Insights с помощью Logic Apps

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на их основе собственные рабочие процессы? Соединитель Application Insights (предварительная версия) для Logic Apps — это то, что вам нужно.

Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. Можно создать приложение логики с соединителем Application Insights, чтобы быстро автоматизировать любой процесс Application Insights. 

Вы также можете добавить дополнительные действия. Компонент Logic Apps службы приложений Azure предоставляет сотни действий. Например, вы можете использовать приложение логики, чтобы автоматически отправить уведомление по электронной почте или создать ошибку в Visual Studio Team Services. Вы можете также воспользоваться одним из множества доступных [шаблонов](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates). С их помощью можно ускорить процесс создания приложения логики. 

## <a name="create-a-logic-app-for-application-insights"></a>Создание приложения логики для Application Insights

В этом руководстве вы узнаете, как создать приложение логики, использующее алгоритм автоматической кластеризации Analytics, для группирования атрибутов в данных для веб-приложения. Отправка результатов по электронной почте — это лишь один из примеров того, как можно совместно использовать Logic Apps и Application Insights Analytics. 

### <a name="step-1-create-a-logic-app"></a>Шаг 1. Создание приложения логики
1. Войдите на [портале Azure](https://portal.azure.com).
2. Щелкните **Создать ресурс**, выберите **Интернет и мобильные устройства**, а затем — **Logic App**.

    ![Окно создания приложения логики](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Шаг 2. Создание триггера для приложения логики
1. В окне **конструктора приложений логики** в разделе **Начинаем с часто используемого триггера** выберите **Повторение**.

    ![Окно конструктора приложений логики](./media/automate-with-logic-apps/logicapp2.png)

2. В поле **Частота** выберите **День**, а в поле **Интервал** введите **1**.

    ![Окно "Повторение" в конструкторе приложений логики](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Шаг 3. Добавление действия Application Insights
1. Выберите поле **+Новый шаг**, а затем щелкните **Добавить действие**.

2. В поле поиска **Выберите действие** введите **Azure Application Insights**.

3. В разделе **Действия** выберите **Azure Application Insights – Visualize Analytics query Preview** (Azure Application Insights — визуализация запроса Analytics [предварительная версия]).

    ![Окно "Выберите действие" в конструкторе приложений логики](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано ниже.

![Идентификатор приложения на портале Azure](./media/automate-with-logic-apps/appid.png) 

Укажите имя подключения, а также идентификатор приложения и ключ API.

![Окно подключения последовательности в конструкторе приложений логики](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Шаг 5. Указание запроса Analytics и типа диаграммы
В следующем примере выбираются невыполненные запросы за последний день. Они сопоставляются с исключениями, которые возникли в рамках операции. Analytics сопоставляет такие запросы на основе идентификатора operation_Id. Затем запрос разделяет результаты с помощью алгоритма автоматической кластеризации. 

При создании собственных запросов убедитесь, что они работают должным образом в Analytics, прежде чем добавить их в последовательность.

1. В поле **Запрос** добавьте следующий запрос Analytics: 

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

2. В поле **Тип диаграммы** выберите **Таблица HTML**.

    ![Окно настройки запроса Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Шаг 6. Настройка приложения логики для отправки сообщения электронной почты

1. Щелкните **Новый шаг**, а затем — **Добавить действие**.

2. В поле поиска введите **Office 365 Outlook**.

3. Щелкните **Office 365 Outlook – Send an email** (Office 365 Outlook — отправка сообщения электронной почты).

    ![Выбор Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. В окне **Отправка сообщения электронной почты** выполните следующие действия:

   a. Введите адрес электронной почты получателя.

   Б. Введите тему сообщения.

   c. Щелкните в любом месте в поле **Текст**, затем в открывшемся справа меню динамического содержимого выберите **Текст**.

   d. Щелкните **Показать дополнительные параметры**.

      ![Конфигурация Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. В меню динамического содержимого выполните следующее.

    a. Выберите **Имя вложения**.

    Б. Выберите **Содержимое вложения**.
    
    c. В поле **Является HTML** выберите значение **Да**.

      ![Экран настройки электронного сообщения Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Шаг 7. Сохранение и тестирование приложения логики
* Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

Можно либо подождать, пока триггер не запустит приложение логики, либо запустить его немедленно, щелкнув **Запуск**.

![Экран создания приложения логики](./media/automate-with-logic-apps/step7.png)

При выполнении приложения логики получатели, указанные в списке рассылки, получат сообщение электронной почты, которое выглядит следующим образом:

![Сообщение электронной почты приложения логики](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о создании [запросов Analytics](app-insights-analytics-using.md).
- Дополнительные сведения о [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





