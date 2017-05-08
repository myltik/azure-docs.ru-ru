---
title: "Примеры запросов для распространенных шаблонов использования Stream Analytics | Документация Майкрософт"
description: "Общие шаблоны запросов Azure Stream Analytics  "
keywords: "примеры запросов"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 976e765a906896c236497d074ca2a8c5fb1f1c53
ms.lasthandoff: 05/01/2017


---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Примеры запросов для распространенных шаблонов использования Stream Analytics
## <a name="introduction"></a>Введение
Запросы в Azure Stream Analytics выражаются с помощью SQL-подобного языка запросов, который описан в [справке по языку запросов Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  В этой статье описаны решения для нескольких стандартных шаблонов запросов на основе реальных сценариев.  Документ не завершен и будет дополняться новыми шаблонами на постоянной основе.

## <a name="query-example-data-type-conversions"></a>Пример запроса: преобразования типов данных
**Описание**: определите типы свойств для входного потока.
Например, вес автомобиля поступает по входному потоку как строка и должен быть преобразован в тип INT для выполнения операции SUM.

**Входные данные**

| Убедитесь, | Время | Вес |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Выходные данные**:

| Убедитесь, | Вес |
| --- | --- |
| Honda |3000 |

**Решение**

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Объяснение.** Используйте оператор CAST для поля Weight, чтобы указать его тип (см. список поддерживаемых типов данных [здесь](https://msdn.microsoft.com/library/azure/dn835065.aspx)).

## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Пример запроса: использование функции Like/Not like для сопоставления шаблонов
**Описание**: проверьте, соответствует ли значение поля в событии определенному шаблону, например "Вернуть номерные знаки, которые начинаются с A и заканчиваются на 9".

**Входные данные**

| Убедитесь, | LicensePlate | Время |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Выходные данные**:

| Убедитесь, | LicensePlate | Время |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Решение**

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Объяснение**: используйте оператор LIKE, чтобы убедиться, что значение поля LicensePlate начинается с A, затем включает любую строку из нуля или более символов и заканчивается на 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Пример запроса: укажите логику для различных случаев и значений (операторы CASE)
**Описание**: укажите разные вычисления для поля на основе определенных условий.
Например, укажите строковое описание для количества переданных автомобилей одной и той же марки с особым условием для значения 1.

**Входные данные**

| Убедитесь, | Время |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Выходные данные**:

| CarsPassed | Время |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyota |2015-01-01T00:00:10.0000000Z |

**Решение**

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Объяснение**: выражение CASE позволяет нам предоставить различные вычисления на основе определенных критериев (в нашем случае это число автомобилей в окне статистики).

## <a name="query-example-send-data-to-multiple-outputs"></a>Пример запроса: отправка данных на несколько выходов
**Описание**: отправьте данные в несколько целевых объектов выходных данных из одного задания.
Например, проанализируйте данные для оповещения на основе пороговых значений и заархивируйте все события в хранилище BLOB-объектов.

**Входные данные**

| Убедитесь, | Время |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Выходные данные 1**:

| Убедитесь, | Время |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Выходные данные 2**:

| Убедитесь, | Время | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Решение**

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Объяснение.** Выражение INTO сообщает Stream Analytics, в которые выходные данные записывать данные из этого оператора.
Первый запрос является передачей полученных данных в выходные данные с именем ArchiveOutput.
Второй запрос выполняет некоторую простую статистическую обработку и фильтрацию и отправляет результаты нижестоящей системе оповещений.
*Примечание.* Вы можете также повторно использовать результаты обобщенных табличных выражений (т. е. операторов WITH) в нескольких операторах выходных данных — это дает дополнительное преимущество, так как для источника входных данных открывается меньше модулей чтения.
Например, 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Пример запроса: подсчет уникальных значений
**Описание**: подсчитайте количество уникальных значений поля, которые отображаются в потоке в течение определенного интервала.
Например, сколько уникальных марок автомобилей проходят через пункт взимания дорожного сбора за 2 секунды?

**Входные данные**

| Убедитесь, | Время |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Выходные данные:**

| Count | Время |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Решение.**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Объяснение.** COUNT(DISTINCT Make) возвращает количество уникальных значений в столбце "Марка" в течение определенного интервала.

## <a name="query-example-determine-if-a-value-has-changed"></a>Пример запроса: определение изменения значения
**Описание.** Рассмотрите предыдущее значение, чтобы определить, отличается ли оно от текущего. Например, предыдущий автомобиль на платной дороге той же марки, что и текущий автомобиль?

**Входные данные**

| Убедитесь, | Время |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Выходные данные**:

| Убедитесь, | Время |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Решение**

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Объяснение**: используйте LAG, чтобы заглянуть в поток входных данных на одно событие назад и получить значение Make. Затем сравните его с Make в текущем событии и поместите событие в выходные данные, если они отличаются.

## <a name="query-example-find-first-event-in-a-window"></a>Пример запроса: поиск первого события в окне
**Описание**: находить новый автомобиль каждые 10 минут?

**Входные данные**

| LicensePlate | Убедитесь, | Время |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Выходные данные**:

| LicensePlate | Убедитесь, | Время |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Решение**

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Теперь изменим проблему и найдем первый автомобиль определенной марки за каждые 10 минут.

| LicensePlate | Убедитесь, | Время |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Решение**

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Пример запроса: поиск последнего события в окне
**Описание**: найдите последний автомобиль за каждые 10 минут.

**Входные данные**

| LicensePlate | Убедитесь, | Время |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Выходные данные**:

| LicensePlate | Убедитесь, | Время |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Решение**

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Объяснение**: запрос состоит из двух этапов — первый находит последнюю отметку времени в 10-минутных окнах. Второй объединяет результаты первого запроса с исходным потоком, чтобы найти события, соответствующие последней отметке времени в каждом окне. 

## <a name="query-example-detect-the-absence-of-events"></a>Пример запроса: обнаружение отсутствия событий
**Описание**: убедитесь, что в потоке отсутствует значение, соответствующее определенным условиям.
Например, два автомобиля одной и той же марки подряд въезжают на платную дорогу в течение 90 секунд.

**Входные данные**

| Убедитесь, | LicensePlate | Время |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Выходные данные**:

| Убедитесь, | Время | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Решение**

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Объяснение**: используйте LAG, чтобы заглянуть в поток входных данных на одно событие назад и получить значение Make. Затем сравните его со значением Make текущего события и поместите событие в выходные данные, если они совпадают, и используйте LAG для получения данных о предыдущем автомобиле.

## <a name="query-example-detect-duration-between-events"></a>Пример запроса: определение промежутка между событиями
**Описание**: найдите длительность заданного события. Например, на основе сведений о посещениях веб-сайта определите время, затраченное на использование функции.

**Входные данные**  

| Пользователь | Функция | Событие | Время |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Начало |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Выходные данные**:  

| Пользователь | Функция | Длительность |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Решение**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Объяснение**: функция LAST позволяет получить последнее значение времени (Time) для события типа Start. Обратите внимание: для обозначения того, что результат будет вычисляться для каждого уникального пользователя, функция LAST включает параметр PARTITION BY [user].  Максимальный промежуток между событиями Start (Пуск) и Stop (Остановка) в запросе составляет 1 час, но при необходимости это значение можно изменить (LIMIT DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Пример запроса: определение продолжительности условия
**Описание.** Определите продолжительность условия.
Предположим, произошла ошибка, которая привела к неправильному отображению массы всех автомобилей (больше 9 тонн (20 000 фунтов)). Необходимо вычислить длительность ошибки.

**Входные данные**

| Убедитесь, | Время | Вес |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Выходные данные**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Решение**

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Объяснение.** Функция LAG позволяет просмотреть входной поток данных за 24-часовой период и найти экземпляры, в которых StartFault и StopFault связаны весом менее 20 000.

## <a name="query-example-fill-missing-values"></a>Пример запроса: заполнить отсутствующие значения
**Описание.** Для потока событий с отсутствующими значениями создайте поток событий с регулярными интервалами.
Например, создавайте каждые 5 секунд событие, которое будет сообщать самую последнюю видимую точку данных.

**Входные данные**

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Выходные данные (первые 10 строк)**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Решение**

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Объяснение.** Этот запрос генерирует события каждые 5 секунд и выводит последнее событие, полученное ранее. Длительность ["прыгающего"](https://msdn.microsoft.com/library/dn835041.aspx ""Прыгающее окно" — Azure Stream Analytics") окна определяет, насколько далеко в прошлое уходит запрос в поисках последнего события (300 секунд в этом примере).

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


