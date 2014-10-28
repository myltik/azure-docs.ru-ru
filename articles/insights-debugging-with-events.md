<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# Мониторинг событий, влияющих на используемые ресурсы или группы ресурсов Azure

1.  Выполните вход на [портал Azure (предварительная версия)][портал Azure (предварительная версия)].
2.  Нажмите кнопку **Обзор** на левой навигационной панели (также называемой **панелью переходов**).
    ![Browse Hub][Browse Hub]
3.  После этого выберите любой ресурс (в зависимости от того, какие события вам интересны). Для иллюстраций снимки экрана в этом документе содержат события для групп ресурсов.
4.  На выноске **Группы ресурсов** щелкните имя группы ресурсов. Это откроет выноску группы ресурсов.
    ![Resource groups][Resource groups]
5.  Выноска группы ресурсов содержит часть, называемую **События за прошлую неделю**. Каждая из полос этой части показывает количество событий, произошедших в каждый из дней прошедшей недели. Каждая из панелей может иметь два различных цвета: синий и розовый. Розовый цвет представляет события **С ошибкой** для данного дня, а синий — все остальные события.
    ![Resource groups][1]
6.  Теперь щелкните часть **События за прошлую неделю**. Появится новая выноска **События**, содержащая все события прошедшей недели, влияющие на ваши группы ресурсов.
    ![Resource groups][2]
7.  Щелкните мышью по одному из событий.  
    ![Resource groups][3]  
    Откроется новая выноска, в которой будут отображаться сведения о событии. Для событий **С ошибкой** эта страница обычно содержит разделы **Дополнительный статус** и **Свойства** с полезными для отладки данными.

  [портал Azure (предварительная версия)]: https://portal.azure.com/
  [Browse Hub]: ./media/insights-debugging-with-events/Insights_Browse.png
  [Resource groups]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [3]: ./media/insights-debugging-with-events/Insights_EventDetails.png
