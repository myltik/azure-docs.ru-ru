---
title: "Автоматизация процессов Azure Application Insights с помощью Microsoft Flow"
description: "Узнайте, как можно использовать Microsoft Flow для быстрой автоматизации повторяющихся процессов с помощью соединителя Application Insights для службы Flow."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Автоматизация процессов Application Insights с помощью соединителя для Microsoft Flow

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на основе них собственные рабочие процессы? Соединитель Application Insights (предварительная версия) для Microsoft Flow — это то, что вам нужно.
Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. После создания последовательности с помощью действия Application Insights будет автоматически запущен запрос Application Insights Analytics. Вы также можете добавить дополнительные действия. Служба Flow предоставляет сотни действий. Например, вы можете использовать Flow, чтобы автоматически отправить уведомление по электронной почте или создать ошибку в Visual Studio Team Services. Вы также можете воспользоваться одним из нескольких [шаблонов](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights), доступных для соединителя для Microsoft Flow. С помощью этих шаблонов можно ускорить процесс создания последовательности. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>Руководство по созданию последовательности для Application Insights

В этом руководстве вы узнаете, как создать последовательность, использующую алгоритм автоматической кластеризации Analytics, для группирования атрибутов данных для веб-приложения. Последовательность автоматически отправляет результаты по электронной почте. Это лишь один пример того, как можно совместно использовать Microsoft Flow и Application Insights Analytics. 

### <a name="step-1-create-a-flow"></a>Шаг 1. Создание последовательности
1. Выполните вход по адресу http://flow.microsoft.com и выберите **My Flows** (Мои последовательности).
2. Щелкните **Create a flow from blank** (Создать последовательность с нуля).

### <a name="step-2-create-a-trigger-for-your-flow"></a>Шаг 2. Создание триггера для последовательности
1. Выберите **Расписание**, а затем **Schedule - Recurrence** (Расписание — Периодичность).
2. В поле **Частота** выберите "День", а в поле **Интервал** введите 1.

![Диалоговое окно триггера последовательности](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>Шаг 3. Добавление действия Azure Application Insights
1. Щелкните **Новый шаг**, а затем — **Добавить действие**.
2. Выполните поиск Azure Application Insights.
3. Выберите "Azure Application Insights – Visualize Analytics query Preview" (Azure Application Insights — визуализация запроса Analytics (предварительная версия)).

![Экран запуска запроса Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

**Предварительные требования**

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано на следующей схеме:

![Идентификатор приложения на портале Azure](./media/app-insights-automate-with-flow/appid.png) 

- Укажите имя подключения, а также идентификатор приложения и ключ API.

![Экран подключения последовательности](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Шаг 5. Указание запроса Analytics и типа диаграммы
В этом примере выбираются невыполненные запросы за последний день. Они сопоставляются с исключениями, которые возникли в рамках операции. Analytics выполняет сопоставление на основе идентификатора operation_Id. Затем запрос разделяет результаты с помощью алгоритма автоматической кластеризации. При создании собственных запросов убедитесь, что они работают должным образом в Analytics, прежде чем добавить их в последовательность.

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
![Экран настройки запроса Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Шаг 6. Настройка последовательности для отправки электронной почты

1. Щелкните **Новый шаг**, а затем — **Добавить действие**.
2. Выполните поиск Office 365 Outlook.
3. Щелкните "Office 365 Outlook – Send an email" (Office 365 Outlook — отправка электронного сообщения).
![Выбор Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. В действии электронного сообщения добавьте следующие данные.
 - Задайте адрес электронной почты получателя.
 - Укажите тему электронного сообщения.
 - Поместите курсор в поле **текста** и в открывшемся справа меню динамического содержимого выберите **Текст**.
 - Щелкните **Показать дополнительные параметры**.

 ![Конфигурация Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. В меню динамического содержимого выполните следующее.
- Выберите **Имя вложения**.
- Выберите **Содержимое вложения**.
- В поле **Является HTML** выберите значение **Да**.

![Экран настройки электронного сообщения Office 365](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>Шаг 7. Сохранение и тестирование последовательности
- Укажите имя последовательности и выберите **Create flow** (Создать последовательность).

![Экран создания Microsoft Flow](./media/app-insights-automate-with-flow/flow8.png)

Вы можете подождать, пока триггер запустит действие, или немедленно запустить последовательность, [запустив триггер по запросу](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

При выполнении последовательности получатели, указанные в списке рассылки, получат электронное письмо, которое выглядит следующим образом:

![Пример электронного сообщения](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о создании [запросов Analytics](app-insights-analytics-using.md).
- Дополнительные сведения о [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






