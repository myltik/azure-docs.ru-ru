---
title: "Панель мониторинга Power BI в Stream Analytics | Документация Майкрософт"
description: "Используйте панель мониторинга Power BI для потоковой передачи, чтобы собирать данные бизнес-аналитики и анализировать большие объемы данных из задания Stream Analytics."
keywords: "панель мониторинга аналитики, панель мониторинга в режиме реального времени"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Azure Stream Analytics и Power BI: панель оперативного мониторинга аналитики для визуализации потоковой передачи данных в режиме реального времени
Azure Stream Analytics позволяет воспользоваться одним из лучших инструментов бизнес-аналитики, Microsoft Power BI. Узнайте, как использовать Azure Stream Analytics для анализа больших объемов потоковых данных и получения подробных аналитических данных на панели мониторинга аналитики Power BI в режиме реального времени.

Используйте [Microsoft Power BI](https://powerbi.com/) для быстрого создания динамической панели мониторинга. [Видео, иллюстрирующее этот сценарий](https://www.youtube.com/watch?v=SGUpT-a99MA).

В этой статье рассматривается создание пользовательских инструментов бизнес-аналитики с помощью Power BI в качестве места назначения для выходных данных заданий Azure Stream Analytics и использование панели мониторинга в режиме реального времени.

## <a name="prerequisites"></a>Предварительные требования
* Учетная запись Microsoft Azure
* Рабочая или учебная учетная запись для Power BI
* Выполните сценарий для начала работы [Выявление мошенничества в реальном времени](stream-analytics-real-time-fraud-detection.md). Данная статья основана на этом рабочем процессе, в который добавлен выходной набор данных потоковой передачи Power BI.

## <a name="add-power-bi-output"></a>Добавление Power BI в качестве места назначения выходных данных
Теперь, когда для задания есть входные данные, можно определить вывод в Power BI. Выберите поле в середине раздела **Выходные данные** панели мониторинга для задания. Нажмите привычную кнопку **+ Добавить** и создайте выходные данные.

![Добавление выходных данных](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

Укажите значение **Выходной псевдоним**. Можно указать любой запоминающийся псевдоним выходных данных. Его особенно удобно использовать, если у задания должно быть несколько выходов. В этом случае такой выход необходимо указывать в запросе. Например, давайте используем псевдоним выходных данных StreamAnalyticsRealTimeFraudPBI.

Нажмите кнопку **Авторизовать**.

![Добавление авторизации](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Появится окно для ввода учетных данных Azure (рабочей или учебной учетной записи) и предоставления заданию Azure доступа к вашей области Power BI.

![Поля авторизации](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

Окно авторизации исчезнет, если введенные данные будут приняты. После чего в новой области вывода появятся поля для имени набора данных и имени таблицы.

![Рабочая область Power BI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

Определите их следующим образом.
* **Рабочая область группы** — в клиенте Power BI выберите рабочую область, в которой будет создан набор данных.
* **Имя набора данных** — укажите имя набора данных, которое необходимо назначить выходным данным для Power BI. Например, давайте укажем StreamAnalyticsRealTimeFraudPBI.
* **Имя таблицы** — задайте имя таблицы в наборе выходных данных Power BI. Допустим, назовем ее StreamAnalyticsRealTimeFraudPBI. В настоящее время для вывода выходных данных из заданий Stream Analytics в Power BI можно использовать только одну таблицу в наборе данных.

Щелкните **Создать**, чтобы завершить настройку выходных данных.

> [!WARNING]
> Учтите, что, если в Power BI уже есть набор данных и таблица с именем, аналогичным указанному вами в этом задании Stream Analytics, то существующие данные будут перезаписаны.
> Кроме того, не следует создавать этот набор данных и таблицу явным образом в своей учетной записи Power BI. Они будут автоматически созданы при запуске задания Stream Analytics, когда оно начнет вносить выходные данные в Power BI. Если ваш запрос задания не возвращает никаких результатов, набор данных и таблица не создаются.
> 
> 

Набор данных создается со следующими установленными параметрами:
* defaultRetentionPolicy: BasicFIFO: для данных используется метод FIFO, максимальное число строк — 200 тыс.;
* defaultMode: pushStreaming: поддержка элементов потоковой передачи и традиционных визуальных объектов для отчетов (так называемые push-уведомления);
* создание наборов данных с другими флагами в настоящее время не поддерживается.

Дополнительные сведения о наборах данных Power BI см. в справочнике по [REST API Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Составление запросов
Откройте в задании вкладку **Запрос** . Составьте запрос, выходные данные которого нужно вывести в Power BI. Например, это может быть SQL-запрос для выявления мошенничества с SIM-картами в сфере телекоммуникаций, пример которого приведен ниже.


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Создание панели мониторинга в Power BI

Перейдите на сайт [Powerbi.com](https://powerbi.com) и войдите, используя свою рабочую или учебную учетную запись. Если результаты запроса задания Stream Analytics были выведены, вы увидите уже созданный набор данных:

![Набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

Щелкните элемент "Добавить" и выберите пользовательские данные потоковой передачи.

![Пользовательский набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

Затем выберите свой набор данных из списка.

![Ваш набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

Теперь создайте карту визуализации и выберите поле fraudulentcalls.

![Добавление счетчика попыток мошенничества](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

Теперь у нас есть счетчик попыток мошенничества!

![Счетчик попыток мошенничества](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

Еще раз выполните упражнение для добавления элемента, но на этот раз выберите график. Добавьте значение fraudulentcalls и ось windowend. В данном случае выбран период за последние 10 минут.

![Мошеннические вызовы](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Обратите внимание, что в этом учебнике показано, как создать только один из возможных видов диаграмм для набора данных. Power BI позволяет создавать другие клиентские инструменты бизнес-аналитики для вашей организации. С еще одним примером панели мониторинга Power BI можно ознакомиться в видео [Приступая к работе с Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Дополнительные сведения о настройке выходных данных Power BI и использовании групп Power BI см. в разделе [Power BI](stream-analytics-define-outputs.md#power-bi) статьи [Выходные данные Stream Analytics: возможности хранения и анализа](stream-analytics-define-outputs.md "Выходные данные Stream Analytics: возможности хранения и анализа"). Дополнительные сведения о создании панелей мониторинга в Power BI см. в статье [Панели мониторинга в Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Ограничения и рекомендации
В Power BI используются как ограничения параллелизма, так и ограничения пропускной способности, как описано на странице по этому адресу: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Цены на использование Power BI")

В настоящее время Power BI может вызываться примерно один раз в секунду. Визуальные элементы потоковой передачи поддерживает пакеты размером в 15 КБ. При превышении этого размера произойдет сбой визуальных элементов потоковой передачи (но push-уведомления продолжат работать).

Из-за этих ограничений Power BI лучше всего подходит для случаев, когда Azure Stream Analytics значительно уменьшает объем загружаемых данных.
Мы советуем использовать TumblingWindow или HoppingWindow, чтобы отправка данных выполнялась не чаще 1 раза в секунду и чтобы ваш запрос удовлетворял требованиям к пропускной способности. Для вычисления значения периода в секундах можно применить следующую формулу:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Пример. Если у вас есть 1 000 устройств, отправляющих данные каждую секунду (это означает, что вы используете SKU Power BI Pro, поддерживающий 1 000 000 строк в час), и вам необходимо рассчитать средний объем передаваемых данных на одно устройство в Power BI при максимальной периодичности отправки данных раз в 4 секунды (как показано ниже):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Это означает, что нам необходимо изменить исходный запрос указанным ниже образом.

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Обновление авторизации
Необходимо повторно выполнить аутентификацию учетной записи Power BI, если с момента создания задания или последней аутентификации пароль был изменен. Если в клиенте Azure Active Directory (AAD) настроена Multi-Factor Authentication (MFA), вам также потребуется каждые две недели обновлять авторизацию Power BI. Признаком этой проблемы является отсутствие выходных данных задания и наличие записи "Authenticate user error" (Ошибка аутентификации пользователя) в журналах операций.

Если задание пытается запуститься, когда срок действия маркера истек, возникнет ошибка и произойдет сбой запуска задания. Чтобы устранить эту проблему, остановите выполнение задания и перейдите к выходным данным Power BI.  Щелкните ссылку "Обновить авторизацию" и перезапустите задание с момента его последней остановки во избежание потери данных. После обновления авторизации с помощью Power BI в области авторизации появится оповещение зеленого цвета о том, что проблема устранена.

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


