---
title: "Автоматизируйте процессы Azure Application Insights с помощью приложения логики Azure."
description: "Узнайте, как можно быстро автоматизировать повторяющиеся процессы, добавив соединитель Application Insights в приложение логики Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>Автоматизация процессов Application Insights с помощью приложения логики Azure

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на основе них собственные рабочие процессы? Соединитель Application Insights (предварительная версия) для Azure Logic Apps — это то, что вам нужно.
Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. Можно создать приложение логики с соединителем Application Insights, чтобы быстро автоматизировать любой процесс Application Insights. Вы также можете добавить дополнительные действия. Logic Apps предоставляет сотни действий. Например, вы можете использовать приложение логики, чтобы автоматически отправить уведомление по электронной почте или создать ошибку в Visual Studio Team Services. Вы можете также воспользоваться одним из множества [шаблонов](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates), доступных для Logic Apps. С помощью этих шаблонов можно ускорить процесс создания приложения логики. 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>Руководство по созданию приложения логики Azure для Application Insights

В этом руководстве вы узнаете, как создать приложение логики Azure, использующее алгоритм автоматической кластеризации Analytics, для группирования атрибутов в данных для веб-приложения. Последовательность автоматически отправляет результаты по электронной почте. Это лишь один пример того, как можно совместно использовать Azure Logic Apps и аналитику Application Insights. 

### <a name="step-1-create-a-logic-app"></a>Шаг 1. Создание приложения логики
1. Войдите на портал: https://portal.azure.com.
2. Создайте приложение логики, щелкнув "Создать" > "Интернет и мобильные устройства".

![Экран создания приложения логики](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Шаг 2. Создание триггера для приложения логики
1.  В конструкторе приложений логики в разделе "Начинаем с часто используемого триггера" выберите "Повторение".
2.  В поле "Частота" выберите "День", а в поле "Интервал" введите 1.

![Диалоговое окно триггера приложения логики](./media/automate-with-logic-apps/logicapp2.png)

![Диалоговое окно частоты для приложения логики](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>Шаг 3. Добавление действия Azure Application Insights
1. Щелкните **Новый шаг**, а затем — **Добавить действие**.
2. Выполните поиск Azure Application Insights.
3. Выберите "Azure Application Insights – Visualize Analytics query Preview" (Azure Application Insights — визуализация запроса Analytics (предварительная версия)).

![Экран запуска запроса Analytics](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

**Предварительные требования**

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано на следующей схеме.

![Идентификатор приложения на портале Azure](./media/automate-with-logic-apps/appid.png) 

- Укажите имя подключения, а также идентификатор приложения и ключ API.

![Экран подключения последовательности](./media/automate-with-logic-apps/flow3.png)

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
![Экран настройки запроса Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>Шаг 6. Настройка последовательности для отправки электронного сообщения

1. Щелкните **Новый шаг**, а затем — **Добавить действие**.
2. Выполните поиск Office 365 Outlook.
3. Щелкните "Office 365 Outlook – Send an email" (Office 365 Outlook — отправка электронного сообщения).
![Выбор Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. В действии электронного сообщения добавьте следующие данные.
 - Задайте адрес электронной почты получателя.
 - Укажите тему электронного сообщения.
 - Поместите курсор в поле **текста** и в открывшемся справа меню динамического содержимого выберите **Текст**.
 - Щелкните **Показать дополнительные параметры**.

 ![Конфигурация Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. В меню динамического содержимого выполните следующее.
- Выберите **Имя вложения**.
- Выберите **Содержимое вложения**.
- В поле **Является HTML** выберите значение **Да**.

![Экран настройки электронного сообщения Office 365](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>Шаг 7. Сохранение и тестирование приложения логики
1. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.
1. Можно либо подождать, пока триггер не запустит приложение логики, либо запустить его немедленно, щелкнув **Запуск**.

![Экран создания приложения логики](./media/automate-with-logic-apps/step7.png)

При выполнении приложения логики получатели, указанные в списке рассылки, получат электронное сообщение, которое выглядит следующим образом.

![Электронное сообщение приложения логики](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о создании [запросов Analytics](app-insights-analytics-using.md).
- Узнайте больше о [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






