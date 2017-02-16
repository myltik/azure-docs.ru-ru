---
title: "Определяемые пользователем функции JavaScript в Azure Stream Analytics | Документация Майкрософт"
description: "Теги и потоки данных датчиков IoT: обработка данных с использованием Stream Analytics в режиме реального времени"
keywords: "решение Интернета вещей, начало работы с Интернетом вещей, инструменты"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Определяемые пользователем функции JavaScript в Azure Stream Analytics
Azure Stream Analytics поддерживает определяемые пользователем функции (UDF), написанные на языке JavaScript. Благодаря широкому набору методов String, RegExp, Math, Array и Date в JavaScript стало проще создавать сложные преобразования данных с помощью заданий Stream Analytics.

## <a name="overview"></a>Обзор
Определяемые пользователем функции JavaScript поддерживает скалярные вычислительные функции без отслеживания состояния, не требующие внешнего подключения. Возвращаемое значение функции может быть только скалярным (одиночным). После добавления в задание функция может использоваться в любом месте в запросе как встроенная скалярная функция.

Ниже приведено несколько примеров ситуаций, в которых удобно использовать определяемые пользователем функции JavaScript.
* Анализ и обработка строки с помощью функций регулярных выражений, например Regexp_Replace() и Regexp_Extract().
* Декодирование закодированных данных, например преобразование двоичных данных в шестнадцатеричные.
* Математические вычисления с помощью математических функций JavaScript.
* Операции с массивами, такие как сортировка, присоединение, поиск и заполнение.

Ниже приведены действия, которые невозможно выполнить с помощью определяемой пользователем функции JavaScript в Stream Analytics.
* Вызов внешних конечных точек REST, например обратный просмотр IP-адресов или извлечение ссылочных данных из внешнего источника.
* Сериализация или десериализация входных и выходных данных в пользовательский формат сообщения.
* Пользовательские статистические функции

Несмотря на то, что они не запрещены в определении функции, следует избегать использования таких функций, как Date.GetDate() или Math.random(). Эти функции **не** возвращают одинаковый результат при каждом вызове, и служба Azure Stream Analytics не ведет журнал вызовов этих функций и возвращаемых результатов. Поэтому, если функция возвращает отличающиеся результаты для одних и тех же событий, не гарантируется повторяемость при перезапуске задания пользователем или службой Stream Analytics.

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Добавление определяемой пользователем функции JavaScript на портале Azure
Чтобы создать простую определяемую пользователем функцию JavaScript в существующем задании Stream Analytics, выполните следующие действия.

1.  Перейдите на портал Azure.

2.  Найдите задание Stream Analytics, а затем щелкните ссылку функций в разделе **Топология задания**.
 
3.  Появится пустой список существующих функций. Щелкните значок **Добавить**, чтобы добавить новую определяемую пользователем функцию.

4.  В колонке **Новая функция** выберите JavaScript в качестве типа функции, и вы увидите шаблон функции по умолчанию в редакторе.
 
5.  Введите _hex2Int_ в качестве псевдонима определяемой пользователем функции и измените реализацию функции, как показано ниже.

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Нажмите кнопку **Сохранить**, и ваша функция появится в списке функций. 

7.  Щелкните новую функцию **hex2Int**, и сможете проверить ее определение. Обратите внимание, что к псевдонимам всех функций добавляется префикс "UDF". В запросе Stream Analytics потребуется вызывать функцию **с префиксом**, в данном случае как **UDF.hex2Int**.
 
## <a name="calling-javascript-udf-in-a-query"></a>Вызов определяемой пользователем функции JavaScript в запросе

1. Откройте редактор запросов, щелкнув **Запрос** в разделе **Топология задания**. 

2.  Измените запрос и добавьте в него вызов только что добавленной определяемой пользователем функции, как показано ниже.

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Щелкните правой кнопкой мыши входные данные задания, чтобы передать пример файла данных. 
 
4.  Щелкните **Тест**, чтобы проверить запрос.


## <a name="supported-javascript-objects"></a>Поддерживаемые объекты JavaScript
Определяемые пользователем функции JavaScript в Azure Stream Analytics поддерживают стандартные встроенные объекты языка JavaScript. Список объектов представлен на странице [Стандартные встроенные объекты](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Преобразование типов Stream Analytics и JavaScript

Существуют различия между типами, поддерживаемыми в языке запросов Stream Analytics и JavaScript. В следующей таблице перечислены соответствующие преобразования этих типов.


---
Анализ потока | JavaScript
--- | ---
bigint | Число (обратите внимание, что JavaScript может точно представлять только целые числа до 2^53).
DateTime | Дата (обратите внимание, что JavaScript поддерживает только миллисекунды). 
double | Number
nvarchar(MAX) | Строка
Record | Объект
Массив, | Массив,
NULL | Null


Также перечислены преобразования типов JavaScript в Azure Stream Analytics (ASA).

---
JavaScript | Анализ потока
--- | ---
Number | Значение типа bigint, если число округлено и находится между long.MinValue и long.MaxValue. В противном случае — значение типа double.
Дата | DateTime
Строка | nvarchar(MAX)
Объект | Record
Массив, | Массив,
NULL, не определено | NULL
Любой другой тип, например Function, Error и т. п. | Не поддерживается и приведет к ошибке времени выполнения.

## <a name="troubleshooting"></a>Устранение неполадок
Ошибки времени выполнения JavaScript будут считаться неустранимыми и регистрироваться в журнале действий. Чтобы получить журнал с портала Azure, перейдите к заданию и щелкните **Журнал действий**.
 

## <a name="other-javascript-udf-usage-patterns"></a>Другие шаблоны использования определяемых пользователем функций JavaScript

### <a name="writing-nested-json-to-output"></a>Запись вложенных данных JSON в выходные данные
Запись строки JSON в выходные данные является обычной задачей, когда имеется последующий этап обработки результатов, принимающий выходные данные задания Stream Analytics в качестве входных данных, которые должны быть представлены в формате JSON. Приведенный ниже пример вызывает функцию JSON.stringify() для упаковки всех входных пар "имя-значение" и их записи в одно строковое значение в выходных данных. 

### <a name="javascript-udf-definition"></a>Определение определяемой пользователем функции JavaScript

```
function main(x) {
return JSON.stringify(x);
}
```

**Пример запроса**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


