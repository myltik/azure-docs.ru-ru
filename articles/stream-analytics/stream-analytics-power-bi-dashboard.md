---
title: "Панель мониторинга Power BI в Azure Stream Analytics | Документация Майкрософт"
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
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 278f73d9ee3f64727b34b3674cd38eaf5301e675
ms.contentlocale: ru-ru
ms.lasthandoff: 05/01/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics и Power BI. Панель мониторинга для анализа потоковой передачи данных
Azure Stream Analytics позволяет воспользоваться одним из лучших инструментов для бизнес-аналитики, Microsoft Power BI. Узнайте, как использовать Azure Stream Analytics для анализа объемных потоковых данных и получения подробных аналитических данных на панели мониторинга Power BI в режиме реального времени.

Используйте [Microsoft Power BI](https://powerbi.com/) для быстрого создания динамической панели мониторинга. [Посмотрите видео об этом сценарии](https://www.youtube.com/watch?v=SGUpT-a99MA).

Из этой статьи вы узнаете, как создавать пользовательские средства бизнес-аналитики, отображая в Power BI выходные данные заданий Azure Stream Analytics. Вы также узнаете, как использовать панель мониторинга в режиме реального времени.

## <a name="prerequisites"></a>Предварительные требования
* Учетная запись Microsoft Azure.
* Рабочая или учебная учетная запись для Power BI.
* Выполнение сценария из статьи [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md). В этой статье используется рабочий процесс, описанный в [примере о выявлении мошенничества](stream-analytics-real-time-fraud-detection.md). В дополнение к нему мы добавим потоковый набор выходных данных Power BI.

## <a name="add-power-bi-output"></a>Добавление Power BI в качестве места назначения выходных данных
Теперь, когда для задания есть входные данные, можно определить вывод в Power BI.

1. Выберите поле **Выходные данные** в середине панели мониторинга для задания. Выберите **+Добавить**, чтобы создать выходные данные.

    ![Добавление выходных данных](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. Укажите **Псевдоним выхода**. Здесь можно указать любой запоминающийся псевдоним для выхода. Его удобно использовать, если у задания будет несколько выходов. Псевдоним позволит указывать выход в запросе. Например, давайте используем псевдоним выходных данных StreamAnalyticsRealTimeFraudPBI.

3. Выберите **Авторизовать**.

    ![Добавление авторизации](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. Откроется окно, где можно ввести учетные данные Azure (рабочей или учебной учетной записи). Это также позволит заданию Azure получить доступ к зоне Power BI.

    ![Поля авторизации](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. Окно авторизации закроется, когда вы введете все необходимые сведения. Область **Новый выход** содержит поля **Имя набора данных** и **Имя таблицы**.

    ![Рабочая область Power BI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. Определите их следующим образом.
    * **Рабочая область группы**. В клиенте Power BI выберите рабочую область, в которой будет создан набор данных.
    * **Имя набора данных**.Укажите имя набора данных, которое необходимо назначить выходным данным для Power BI. Например, давайте здесь укажем значение StreamAnalyticsRealTimeFraudPBI.
    * **Имя таблицы**. Задайте имя таблицы в наборе выходных данных Power BI. Например, давайте укажем StreamAnalyticsRealTimeFraudPBI. Сейчас для вывода выходных данных из заданий Stream Analytics в Power BI можно использовать только одну таблицу в наборе данных.

7. Нажмите кнопку **Создать**. Итак, настройка выходных данных завершена.

> [!WARNING]
> Если в Power BI уже есть набор данных и таблица с такими именами, которые мы указали в задании Stream Analytics, существующие данные в них будут перезаписаны.
> Мы также не рекомендуем явным образом создавать набор данных и таблицу в учетной записи Power BI. При запуске задания Stream Analytics они будут созданы автоматически, и задание начнет вносить выходные данные для Power BI. Если ваш запрос задания не возвращает никаких результатов, набор данных и таблица не создаются.
>
>

Набор данных создается со следующими параметрами:
* **defaultRetentionPolicy: BasicFIFO**. Для данных используется метод FIFO, максимальное число строк — 200 тыс.;
* **defaultMode: pushStreaming**. Поддержка элементов потоковой передачи и традиционных визуальных объектов для отчетов (данные для отправки).

Сейчас нельзя создавать наборы данных с другими флагами.

Дополнительные сведения о наборах данных Power BI см. в справочнике по [REST API Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Составление запросов
Откройте в задании вкладку **Запрос** . Составьте запрос, выходные данные которого нужно вывести в Power BI. Например, можно использовать подобный SQL-запрос для выявления мошенничества с SIM-картами в сфере телекоммуникаций:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Создание панели мониторинга в Power BI

1. Перейдите на сайт [Powerbi.com](https://powerbi.com) и выполните вход с помощью рабочей или учебной учетной записи. Если запрос, обрабатываемый заданием Stream Analytics, вернет какие-либо данные, вы увидите созданный набор данных:

    ![Набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Щелкните элемент **Добавить плитку** и выберите пользовательские данные потоковой передачи.

    ![Пользовательский набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. Выберите в списке набор данных.

    ![Ваш набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. Создайте карточку визуализации. Выберите поле **fraudulentcalls**.

    ![Добавление счетчика попыток мошенничества](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    Теперь у нас есть счетчик попыток мошенничества!

    ![Счетчик попыток мошенничества](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. Повторите процесс добавления элемента. На этот раз выберите график. Добавьте значение **fraudulentcalls** и ось **windowend**. Для следующего снимка экрана мы выбрали данные за последние 10 минут:

![Мошеннические вызовы](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


В этом руководстве описано создание для набора данных только одного из возможных видов диаграмм. Power BI позволяет создавать другие клиентские инструменты бизнес-аналитики для вашей организации. С еще одним примером панели мониторинга Power BI можно ознакомиться в видео [Приступая к работе с Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Дополнительные сведения о настройке выходных данных Power BI и использовании групп Power BI см. в разделе [Power BI](stream-analytics-define-outputs.md#power-bi) статьи [Выходные данные Stream Analytics: возможности хранения и анализа](stream-analytics-define-outputs.md "Выходные данные Stream Analytics: возможности хранения и анализа"). Также полезная информация доступна в разделе документации с описанием [панелей мониторинга в Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="learn-about-limitations-and-best-practices"></a>Информация об ограничениях и рекомендациях
Power BI использует ограничения как для параллелизма, так и для пропускной способности. Они подробно описаны [на странице информации о Power BI](https://powerbi.microsoft.com/pricing "Power BI Pricing").

Сейчас Power BI можно вызывать примерно один раз в секунду. Визуальные элементы потоковой передачи поддерживают пакеты размером в 15 КБ. При превышении этого порога визуальные элементы потоковой передачи останавливаются (но данные для отправки продолжают обрабатываться).

С учетом этого Power BI лучше всего подходит для случаев, когда Azure Stream Analytics значительно уменьшает объем передаваемых данных.
Мы рекомендуем использовать переворачивающееся или прыгающее окно, чтобы отправка данных происходила не чаще одного раза в секунду, а запрос удовлетворял требованиям к пропускной способности.

Можно использовать следующую формулу для вычисления длительности окна в секундах:

![Выражение 1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Например:
- У вас есть 1000 устройств, отправляющих данные с интервалами в 1 секунду.
- Вы используете лицензию Power BI Pro, которая поддерживает 1 000 000 строк в час.
- Вы хотите публиковать в Power BI средний объем данных на каждом устройстве.

Соответствующее выражение для расчета выглядит так:

![Выражение 2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Это означает, что мы можем изменить исходный запрос таким образом:

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
Если с момента создания задания или последней аутентификации пароль был изменен, необходимо повторно выполнить аутентификацию с учетной записью Power BI. Если в клиенте Azure Active Directory (AAD) настроена многофакторная идентификация Azure, вам потребуется каждые две недели обновлять авторизацию Power BI. Если вы этого не сделаете, возможны такие проблемы, как отсутствие выходных данных задания или "Ошибки аутентификации пользователя" в журнале операций.

Если задание пытается запуститься, когда срок действия маркера уже истек, возникает ошибка и сбой запуска задания. Чтобы устранить эту проблему, остановите выполнение задания и перейдите к выходным данным Power BI. Чтобы избежать потери данных, щелкните ссылку **Обновить авторизацию** и перезапустите задание со **времени последней остановки**.

Когда вы обновите авторизацию в Power BI, в области авторизации появится оповещение зеленого цвета о том, что проблема устранена.

## <a name="get-help"></a>Получение справки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Azure Stream Analytics).
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

