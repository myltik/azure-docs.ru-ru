---
title: Общие шаблоны запросов в Azure Stream Analytics
description: В этой статье приводятся некоторые общие шаблоны и структуры запросов, которые могут использоваться в заданиях Azure Stream Analytics.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: b929eaf17255210a5c813e3e91478f9202941b64
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Примеры запросов для распространенных шаблонов использования Stream Analytics
## <a name="introduction"></a>Введение
Запросы в Azure Stream Analytics выражаются на языке запросов на основе SQL. Эти запросы описаны в руководстве [Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Stream Analytics). В этой статье описаны решения для нескольких стандартных шаблонов запросов на основе реальных сценариев. Документ не завершен и будет дополняться новыми шаблонами на постоянной основе.

## <a name="query-example-convert-data-types"></a>Пример запроса: преобразование типов данных
**Описание.** Определите типы свойств для входного потока.
Например, вес автомобиля поступает по входному потоку как строка и должен быть преобразован в тип **INT** для выполнения операции **SUM**.

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

**Объяснение.** Используйте инструкцию **CAST** в поле **Weight** для указания типа данных. Список поддерживаемых типов данных Azure Stream Analytics см. в [этой статье](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Пример запроса: использование функции Like/Not like для сопоставления шаблонов
**Описание.** Проверьте, соответствует ли значение поля в событии определенному шаблону.
Например, проверьте, возвращает ли результат номерные знаки, которые начинаются с A и заканчиваться на 9.

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

**Объяснение.** Используйте инструкцию **LIKE** для проверки значения поля **LicensePlate**. Оно должно начинаться с А, затем должна идти любая строка из нуля или более символов и, наконец, цифра 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Пример запроса: укажите логику для различных случаев и значений (операторы CASE)
**Описание.** Укажите разные вычисления для поля на основе определенных условий.
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

**Объяснение.** Выражение **CASE** позволяет нам предоставить различные вычисления на основе определенных критериев (в нашем случае это число автомобилей в окне статистики).

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

**Объяснение.** Выражение **INTO** сообщает Stream Analytics, в какие выходные данные записывать данные из этого оператора.
Первый запрос является передачей полученных данных в выходные данные с именем **ArchiveOutput**.
Второй запрос выполняет некоторую простую статистическую обработку и фильтрацию и отправляет результаты нижестоящей системе оповещений.

Обратите внимание, что можно повторно использовать результаты обобщенных табличных выражений (таких как инструкции **WITH**) в нескольких выходных инструкциях. При этом вы получаете дополнительное преимущество, так как нужно открывать меньше читателей в источнике входных данных.
Например:  

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

## <a name="query-example-count-unique-values"></a>Пример запроса: подсчет уникальных значений
**Описание.** Подсчитайте количество уникальных значений поля, которые отображаются в потоке в течение определенного интервала.
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

| CountMake | Время |
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


**Объяснение.**
**COUNT(DISTINCT Make)** возвращает количество уникальных значений в столбце **Марка** в течение определенного интервала.

## <a name="query-example-determine-if-a-value-has-changed"></a>Пример запроса: определение изменения значения
**Описание.** Изучите предыдущее значение, чтобы определить, отличается ли оно от текущего.
Например, предыдущий автомобиль на платной дороге той же марки, что и текущий автомобиль?

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

**Объяснение.** Используйте **LAG**, чтобы заглянуть в поток входных данных на одно событие назад и получить значение **Make**. Затем сравните его со значением **Make** в текущем событии и поместите событие в выходные данные, если они отличаются.

## <a name="query-example-find-the-first-event-in-a-window"></a>Пример запроса: поиск первого события в окне
**Описание.** Найдите первый автомобиль за каждые 10 минут.

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

## <a name="query-example-find-the-last-event-in-a-window"></a>Пример запроса: поиск последнего события в окне
**Описание.** Найдите последний автомобиль за каждые 10 минут.

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

**Объяснение.** Запрос состоит из двух этапов. На первом выполняется поиск последней метки времени в 10-минутном окне, а на втором — объединение результатов первого запроса с исходным потоком, чтобы найти события, соответствующие последней метке времени в каждом окне. 

## <a name="query-example-detect-the-absence-of-events"></a>Пример запроса: обнаружение отсутствия событий
**Описание.** Убедитесь, что в потоке отсутствует значение, соответствующее определенным условиям.
Например, два автомобиля одной и той же марки подряд въезжают на платную дорогу за последние 90 секунд.

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

**Объяснение.** Используйте **LAG**, чтобы заглянуть в поток входных данных на одно событие назад и получить значение **Make**. Сравните его со значением **MAKE** в текущем событии и поместите событие в выходные данные, если они совпадают. **LAG** также можно использовать для получения данных о предыдущем автомобиле.

## <a name="query-example-detect-the-duration-between-events"></a>Пример запроса: определение промежутка между событиями
**Описание**: найдите длительность заданного события. Например, на основе сведений о посещениях веб-сайта определите время, затраченное на использование функции.

**Входные данные**  

| Пользователь | Функция | Событие | Время |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Начало |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Выходные данные**:  

| Пользователь | Функция | Duration |
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

**Объяснение.** Функция **LAST** позволяет получить последнее значение времени **TIME** для события типа **Start**. Для обозначения того, что результат будет вычисляться для каждого уникального пользователя, функция **LAST** включает параметр **PARTITION BY [user]**. Максимальный промежуток между событиями **Start** и **Stop** в запросе составляет 1 час, но при необходимости это значение можно изменить **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Пример запроса: определение продолжительности условия
**Описание.** Определите продолжительность условия.
Предположим, произошла ошибка, которая привела к неправильному отображению массы всех автомобилей (больше 9 тонн). Необходимо вычислить длительность ошибки.

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

**Объяснение.** Функция **LAG** позволяет просмотреть входной поток данных за 24-часовой период и найти экземпляры, в которых **StartFault** и **StopFault** связаны весом менее 20 000.

## <a name="query-example-fill-missing-values"></a>Пример запроса: заполнить отсутствующие значения
**Описание.** Для потока событий с отсутствующими значениями создайте поток событий с регулярными интервалами.
Например, создавайте каждые 5 секунд событие, сообщающее последнюю видимую точку данных.

**Входные данные**

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4. |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Выходные данные (первые 10 строк)**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4. |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4. |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4. |
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


**Объяснение.** Этот запрос создает события каждые 5 секунд и выводит последнее событие, полученное ранее. Длительность ["прыгающего"](https://msdn.microsoft.com/library/dn835041.aspx ""Прыгающее окно" — Azure Stream Analytics") окна определяет, насколько далеко в прошлое уходит запрос в поисках последнего события (300 секунд в этом примере).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Пример запроса: корреляция двух типов событий в одном потоке
**Описание.** Иногда требуется создавать предупреждения, которые основаны на нескольких типах событий, произошедших в определенный диапазон времени.
Например, рассмотрим сценарий IoT для домашних печей. Предположим, нам нужно отображать оповещение, когда температура вентилятора опускается ниже 40 и максимальная мощность за последние 3 минуты не превышает 10.

**Входные данные**

| Twitter в режиме реального | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" (Печь 1) | "temp" (температура) |120 |
| "2018-01-01T16:01:00" | "Oven1" (Печь 1) | "power" (питание) |15 |
| "2018-01-01T16:02:00" | "Oven1" (Печь 1) | "temp" (температура) |100 |
| "2018-01-01T16:02:00" | "Oven1" (Печь 1) | "power" (питание) |15 |
| "2018-01-01T16:03:00" | "Oven1" (Печь 1) | "temp" (температура) |70 |
| "2018-01-01T16:03:00" | "Oven1" (Печь 1) | "power" (питание) |15 |
| "2018-01-01T16:04:00" | "Oven1" (Печь 1) | "temp" (температура) |50 |
| "2018-01-01T16:04:00" | "Oven1" (Печь 1) | "power" (питание) |15 |
| "2018-01-01T16:05:00" | "Oven1" (Печь 1) | "temp" (температура) |30 |
| "2018-01-01T16:05:00" | "Oven1" (Печь 1) | "power" (питание) |8 |
| "2018-01-01T16:06:00" | "Oven1" (Печь 1) | "temp" (температура) |20 |
| "2018-01-01T16:06:00" | "Oven1" (Печь 1) | "power" (питание) |8 |
| "2018-01-01T16:07:00" | "Oven1" (Печь 1) | "temp" (температура) |20 |
| "2018-01-01T16:07:00" | "Oven1" (Печь 1) | "power" (питание) |8 |
| "2018-01-01T16:08:00" | "Oven1" (Печь 1) | "temp" (температура) |20 |
| "2018-01-01T16:08:00" | "Oven1" (Печь 1) | "power" (питание) |8 |

**Выходные данные**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" (Печь 1) |30 | "Short circuit heating elements" (Короткое замыкание нагревательных элементов) |15 |
| "2018-01-01T16:06:00" | "Oven1" (Печь 1) |20 | "Short circuit heating elements" (Короткое замыкание нагревательных элементов) |15 |
| "2018-01-01T16:07:00" | "Oven1" (Печь 1) |20 | "Short circuit heating elements" (Короткое замыкание нагревательных элементов) |15 |

**Решение**

````
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
````

**Объяснение.** В первом запросе `max_power_during_last_3_mins` используется [скользящее окно](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), чтобы найти максимальное значение датчика питания для каждого устройства за последние 3 минут. Второй запрос объединяется с первым запросом, чтобы найти значение питания в последнем окне, которое связано с текущим событием. Затем, если условия выполняются, создается оповещение для устройства.


## <a name="get-help"></a>Получение справки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

