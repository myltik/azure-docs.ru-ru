---
title: Интеграция панели мониторинга Power BI с Azure Stream Analytics
description: В этой статье описывается, как использовать панель мониторинга Power BI в режиме реального времени для визуализации выходных данных задания Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2017
ms.openlocfilehash: e84903870110091d527e870600d9a67bdc9cc6e5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418460"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Руководство. Stream Analytics и Power BI. Панель мониторинга для анализа потоковой передачи данных
Azure Stream Analytics позволяет воспользоваться одним из лучших инструментов для бизнес-аналитики, [Microsoft Power BI](https://powerbi.com/). В этой статье вы узнаете, как создавать средства бизнес-аналитики, отображая в Power BI выходные данные заданий Azure Stream Analytics. Вы также узнаете, как создавать информационные панели и использовать их в режиме реального времени.

Эта статья является продолжением руководства Stream Analytics по [выявлению мошенничества в реальном времени](stream-analytics-real-time-fraud-detection.md). Она создана на основе рабочего процесса, изложенного в этом руководстве. Кроме того, в ней добавлены выходные данные Power BI для визуализации мошеннических телефонных вызовов, обнаруженных заданием Stream Analytics. 

Посмотрите [видео](https://www.youtube.com/watch?v=SGUpT-a99MA) об этом сценарии.


## <a name="prerequisites"></a>предварительным требованиям

Чтобы начать, у вас должны быть следующие компоненты:

* Учетная запись Azure.
* Учетная запись для Power BI. Вы можете использовать рабочую или учебную учетную запись.
* Полная версия руководства по выявлению мошенничества в режиме реального времени представлена [здесь](stream-analytics-real-time-fraud-detection.md). Руководство включает приложение, создающее вымышленные метаданные телефонных вызовов. В этом руководстве описано создание концентратора событий и отправка потоковых данных телефонных вызовов к нему. Вы напишете запрос, определяющий мошеннические вызовы (вызовы с одного и того же номера одновременно из разных мест). 


## <a name="add-power-bi-output"></a>Добавление Power BI в качестве места назначения выходных данных
В руководстве по выявлению мошенничества в реальном времени выходные данные отправлялись в хранилище BLOB-объектов Azure. В этом разделе вы добавите выход, который отправляет сведения в Power BI.

1. На портале Azure откройте задание Streaming Analytics, созданное ранее. Если вы использовали рекомендуемое имя, задание называется `sa_frauddetection_job_demo`.

2. В центре информационной панели задания выберите диалоговое окно **Выходные данные** и щелкните **+ Добавить**.

3. В поле **Выходной псевдоним** введите `CallStream-PowerBI`. Вы можете использовать другое имя. В таком случае запишите его, так как это имя понадобится вам позже. 

4. В поле **Приемник** выберите **Power BI**.

   ![Создание выхода для Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Щелкните **Авторизовать**.

    Откроется окно, где можно ввести учетные данные Azure (рабочей или учебной учетной записи). 

    ![Ввод учетных данных для доступа к Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Введите свои учетные данные. Обратите внимание, что при этом вы даете заданию Stream Analytics разрешение на доступ к области Power BI.

7. Вернувшись в колонку **Новые выходные данные**, введите следующие сведения:

    * **Рабочая область группы.** В клиенте Power BI выберите рабочую область, где необходимо создать набор данных.
    * **Имя набора данных.** Введите `sa-dataset`. Вы можете использовать другое имя. Если вы используете другое имя, запишите его.
    * **Имя таблицы.** Введите `fraudulent-calls`. Сейчас для вывода выходных данных из заданий Stream Analytics в Power BI можно использовать только одну таблицу в наборе данных.

    ![Рабочая область Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Если в Power BI есть набор данных и таблица с такими же именами, которые вы указали в задании Stream Analytics, имеющиеся имена будут перезаписаны.
    > Мы не рекомендуем явным образом создавать набор данных и таблицу в учетной записи Power BI. При запуске задания Stream Analytics они будут созданы автоматически, и задание начнет вносить выходные данные для Power BI. Если ваш запрос задания не возвращает никаких результатов, набор данных и таблица не создаются.
    >

8. Нажмите кнопку **Создать**.

Набор данных создается со следующими параметрами:

* **defaultRetentionPolicy: BasicFIFO**. Для данных используется метод FIFO, максимальное число строк — 200 тыс.;
* **defaultMode: pushStreaming.** Набор данных поддерживает элементы потоковой передачи и традиционные визуальные объекты для отчетов (данные для отправки).

Сейчас нельзя создавать наборы данных с другими флагами.

Дополнительные сведения о наборах данных Power BI см. в справочнике по [REST API Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-the-query"></a>Написание запроса

1. Закройте колонку **Выходные данные** и вернитесь в колонку задания.

2. Щелкните поле **Запрос**. 

3. Введите следующий запрос. Этот запрос аналогичен запросу самосоединения, созданному в руководстве по обнаружению мошенничества. Различие заключается в том, что этот запрос передает результаты в новые выходные данные, созданные вами (`CallStream-PowerBI`). 

    >[!NOTE]
    >Если вы не присвоили имя входным данным `CallStream` в руководстве по обнаружению мошенничества, замените имя `CallStream` в предложениях **FROM** и **JOIN** в запросе.

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. Выберите команду **Сохранить**.


## <a name="test-the-query"></a>Проверка запроса
Этот раздел необязательный, но мы рекомендуем ознакомиться с ним. 

1. Если сейчас приложение TelcoStreaming не запущено, запустите его, сделав следующее:

    * Откройте командное окно.
    * Перейдите в папку, где находятся измененные файлы telcogenerator.exe и telcodatagen.exe.config.
    * Выполните следующую команду:

            telcodatagen.exe 1000 .2 2

2. В колонке **Запрос** щелкните точки рядом с входными данными `CallStream`, а затем выберите **Образец данных со входа**.

3. Укажите, что необходим трехминутный образец данных, и щелкните **ОК**. Дождитесь уведомления о выборке данных.

4. Щелкните **Тест** и убедитесь, что вы получаете результаты.


## <a name="run-the-job"></a>Выполнение задания

1. Убедитесь, что приложение TelcoStreaming выполняется.

2. Закройте колонку **Запрос**.

3. В колонке задания щелкните **Запуск**.

    ![Запуск задания Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Задание Stream Analytics начинает искать мошеннические вызовы во входящем потоке. Задание также создает набор данных и таблицу в Power BI и начинает отправлять данные о мошеннических вызовах к ним.


## <a name="create-the-dashboard-in-power-bi"></a>Создание панели мониторинга в Power BI

1. Перейдите на сайт [Powerbi.com](https://powerbi.com) и войдите с помощью рабочей или учебной учетной записи. Если результаты запроса задания Stream Analytics были выведены, вы увидите уже созданный набор данных:

    ![Набор данных потоковой передачи в Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. В рабочей области щелкните **+&nbsp;Создать**.

    ![Кнопка "Создать" в рабочей области Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Создайте информационную панель и назовите ее `Fraudulent Calls`.

    ![Создание информационной панели и присвоение ей имени в рабочей области Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. В верхней части окна щелкните **Добавить плитку**, выберите **Пользовательские данные потоковой передачи**, а затем щелкните **Далее**.

    ![Пользовательский набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. В области **Your datsets** (Ваши наборы данных) выберите свой набор данных и щелкните **Далее**.

    ![Ваш набор данных потоковой передачи](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. В поле **Тип визуализации** выберите **Карточка**, а затем в списке **Поля** выберите **fraudulentcalls**.

    ![Сведения о визуализации для новой плитки](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Нажмите кнопку **Далее**.

8. Заполните сведения о плитке, такие как заголовок и подзаголовок.

    ![Заголовок и подзаголовок для новой плитки](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Нажмите кнопку **Применить**.

    Теперь у вас есть счетчик попыток совершения мошенничества.

    ![Счетчик попыток мошенничества](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Повторите весь процесс еще раз, чтобы добавить плитку (начиная с шага 4). В этот раз сделайте следующее:

    * В поле **Тип визуализации** выберите **График**. 
    * Добавьте ось и выберите **windowend**. 
    * Добавьте значение и выберите **fraudulentcalls**.
    * В списке **Отображаемый интервал времени** укажите последние 10 минут.

    ![Создание плитки графика](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Щелкните **Далее**, добавьте заголовок и подзаголовок, а затем щелкните **Применить**.

    Теперь на информационной панели Power BI есть два представления данных о мошеннических вызовах, обнаруженных в потоковой передаче данных.

    ![Готовая информационная панель Power BI, отображающая 2 плитки со сведениями о мошеннических вызовах](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Подробнее о Power BI

В этом руководстве демонстрируется, как создать некоторые визуализации для набора данных. Power BI позволяет создавать другие клиентские инструменты бизнес-аналитики для вашей организации. Дополнительные идеи см. в следующих ресурсах:

* С еще одним примером панели мониторинга Power BI можно ознакомиться в видео [Приступая к работе с Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .
* Дополнительные сведения о настройке вывода задания Streaming Analytics в Power BI и использовании групп Power BI см. разделе [Power BI](stream-analytics-define-outputs.md#power-bi) статьи [Выходные данные Stream Analytics: возможности хранения и анализа](stream-analytics-define-outputs.md). 
* Дополнительные сведения об использовании Power BI см. в статье [Панели мониторинга в службе Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Информация об ограничениях и рекомендациях
Сейчас Power BI можно вызывать примерно один раз в секунду. Визуальные элементы потоковой передачи поддерживают пакеты размером в 15 КБ. При превышении этого порога визуальные элементы потоковой передачи останавливаются (но данные для отправки продолжают обрабатываться). С учетом этого Power BI лучше всего подходит для случаев, когда Azure Stream Analytics значительно уменьшает объем передаваемых данных. Мы рекомендуем использовать переворачивающееся или прыгающее окно, чтобы отправка данных происходила не чаще одного раза в секунду, а запрос отвечал требованиям к пропускной способности.

Можно использовать следующую формулу для вычисления длительности окна в секундах:

![Выражение 1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Например: 

* У вас есть 1000 устройств, отправляющих данные с интервалами в 1 секунду.
* Вы используете лицензию Power BI Pro, которая поддерживает 1 000 000 строк в час.
* Вы хотите публиковать в Power BI средний объем данных на каждом устройстве.

Соответствующее выражение для расчета выглядит так:

![Выражение 2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

При такой конфигурации можно изменить исходный запрос на следующий:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Обновление авторизации
Если с момента создания задания или последней аутентификации пароль был изменен, необходимо повторно выполнить аутентификацию с учетной записью Power BI. Если в клиенте Azure Active Directory (AAD) настроена Многофакторная идентификация Azure, вам потребуется каждые две недели обновлять авторизацию Power BI. Если вы этого не сделаете, возможны такие проблемы, как отсутствие выходных данных задания или `Authenticate user error` в журнале операций.

Аналогично, если задание запускается после истечения срока действия маркера, возникает ошибка и происходит сбой задания. Чтобы устранить эту проблему, остановите выполнение задания и перейдите к выходным данным Power BI. Чтобы избежать потери данных, щелкните ссылку **Обновить авторизацию** и перезапустите задание со **времени последней остановки**.

Когда вы обновите авторизацию в Power BI, в области авторизации появится оповещение зеленого цвета о том, что проблема устранена.

## <a name="get-help"></a>Получение справки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Azure Stream Analytics).
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
