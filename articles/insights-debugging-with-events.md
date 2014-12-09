<properties title="How to debug with events" pageTitle="Отладка с помощью событий" description="Learn how to see events in Azure." authors="hanikn" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn" />

# Мониторинг событий, влияющих на используемые ресурсы или группы ресурсов Azure

1. Войдите на [портал Azure (предварительной версии)](https://portal.azure.com/).
2. Нажмите кнопку **Обзор** на левой навигационной панели (также называемой **панелью переходов**).  
    ![Browse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. После этого выберите любой ресурс (в зависимости от того, какие события вам интересны). Для наглядности снимки экрана в этом документе содержат события для групп ресурсов.
4. В колонке **Группы ресурсов** щелкните имя группы ресурсов. Это действие откроет колонку группы ресурсов.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_SelectRG.png)
5. Колонка группы ресурсов содержит часть, называемую **События за прошлую неделю**. Каждая из полос этой части показывает количество событий, произошедших в каждый из дней прошедшей недели. Каждая из панелей может иметь два различных цвета: синий и розовый. Розовый цвет представляет события **с ошибкой** для данного дня, а синий - все остальные события.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_RGBlade.png)
6. Теперь щелкните часть **События за прошлую неделю**. Появится новая колонка **События**, содержащая все события прошедшей недели, влияющие на ваши группы ресурсов.
    ![Resource groups](./media/insights-debugging-with-events/Insights_AllEvents.png)
7. Щелкните одно из событий.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_EventDetails.png)  
    Откроется новая колонка, в которой будет отображаться много сведений о событии. Для событий **с ошибкой** эта страница обычно содержит разделы **Дополнительный статус** и **Свойства** с полезными для отладки данными.

