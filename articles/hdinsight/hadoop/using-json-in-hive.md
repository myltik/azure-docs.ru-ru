---
title: Анализ и обработка документов JSON с помощью Apache Hive в Azure HDInsight | Документация Майкрософт
description: Узнайте, как использовать документы JSON и анализировать их с помощью Apache Hive в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 19d8e74e6f3ca8e7a0b0a64bdd782ea21b233c17
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202532"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Обработка и анализ документов JSON с использованием Apache Hive в Azure HDInsight

Узнайте, как обрабатывать и анализировать файлы JavaScript Object Notation (JSON) с помощью Apache Hive в Azure HDInsight. В этом руководстве используется следующий документ JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Файл можно найти в **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Дополнительные сведения об использовании хранилища BLOB-объектов Azure с HDInsight приведены в разделе [Использование службы хранилища Azure с кластерами Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md). Вы можете скопировать файл в контейнер по умолчанию кластера.

В этом руководстве используется консоль Hive. Инструкции по открытию консоли Hive приведены в разделе [Использование Hive с Hadoop в HDInsight с помощью удаленного рабочего стола](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Документы JSON, преобразованные в плоскую структуру
Методы, перечисленные в следующем разделе требуют, чтобы документ JSON содержал одну строку. Поэтому необходимо преобразовать документ JSON в строку. Если документ JSON уже преобразован, можно пропустить этот шаг и сразу перейти к следующему разделу, посвященному анализу данных JSON. Чтобы преобразовать документ JSON в плоскую структуру, выполните следующий сценарий:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Необработанный файл JSON находится в **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Таблица Hive **StudentsRaw** указывает на необработанный документ JSON, который не имеет плоскую структуру.

Таблица Hive **StudentsOneLine** сохраняет данные в файловой системе по умолчанию HDInsight в каталоге **/json/students/**.

Инструкция **INSERT** заполняет таблицу **StudentOneLine** плоскими данными JSON.

Инструкция **SELECT** возвращает всего одну строку.

Вот результат выполнения инструкции **SELECT**:

![Преобразование документа JSON в плоскую структуру][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Анализ документов JSON в Hive
Hive предоставляет три различных механизма для выполнения запросов к документам JSON (вы также можете написать собственный):

* использование определяемой пользователем функции GET_JSON_OBJECT;
* использование определяемой пользователем функции JSON_TUPLE;
* использование пользовательского сериализатора/десериализатора (SerDe);
* написание собственной определяемой пользователем функции с использованием Python или других языков. Дополнительные сведения о том, как выполнять код Python с Hive, см. в статье [Использование пользовательских функций Python с Hive и Pig в Azure HDInsight][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Использование определяемой пользователем функции GET_JSON_OBJECT
В Hive есть встроенная определяемая пользователем функция под названием [ET_JSON_OBJECT](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), которая может выполнять запросы JSON во время выполнения. Этот метод принимает два аргумента — имя таблицы, которая содержит плоский документ JSON, и поле JSON, которое требуется проанализировать. Рассмотрим на примере, как работает эта пользовательская функция.

Следующий запрос возвращает имя и фамилию каждого студента:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Вот какие результаты дает выполнение этого запроса в окне консоли:

![Пользовательская функция get_json_object][image-hdi-hivejson-getjsonobject]

У определяемой пользователем функции GET_JSON_OBJECT есть некоторые ограничения.

* Так как каждое поле в запросе требует повторного синтаксического анализа запроса, это влияет на производительность.
* **GET\_JSON_OBJECT()** возвращает строковое представление массива. Для преобразования этого массива в массив Hive необходимо использовать регулярные выражения для замены квадратных скобок "[" и "]", а затем также выполнить разбиение для получения массива.

Именно поэтому в вики Hive рекомендуется использовать JSON_TUPLE.  

### <a name="use-the-jsontuple-udf"></a>Использование определяемой пользователем функции JSON_TUPLE
Еще одна определяемая пользователем функция в Hive называется [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), и она дает лучшие результаты по сравнению с [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Этот метод принимает набор ключей и строку JSON, а затем возвращает кортеж из значений, используя одну функцию. Следующий запрос возвращает идентификатор студента и его оценку из документа JSON.

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Выходные данные этого сценария в консоли Hive:

![Пользовательская функция json_tuple][image-hdi-hivejson-jsontuple]

Определяемая пользователем функция JSON_TUPLE использует синтаксис [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) Hive, который позволяет JSON\_TUPLE создать виртуальную таблицу, применяя определяемую пользователем функцию к каждой строке исходной таблицы. Из-за многократного использования **LATERAL VIEW** синтаксис сложных документов JSON становится слишком громоздким. Кроме того, **JSON_TUPLE** не может обрабатывать вложенные документы JSON.

### <a name="use-a-custom-serde"></a>Использование пользовательского SerDe
SerDe отлично подходит для синтаксического анализа вложенных документов JSON. Он позволяет определить схему JSON, а затем использовать ее для синтаксического анализа документов. См. инструкции по [использованию пользовательского формата SerDe JSON с Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Сводка
Напоследок следует заметить, что тип оператора JSON в Hive, который нужно выбрать, зависит от ситуации. Если вам требуется найти всего одно поле в простом документе JSON, можно применять определяемую пользователем функцию Hive GET_JSON_OBJECT. Если нужно выполнить поиск по нескольким ключам, можно использовать json_tuple. При наличии вложенного документа следует использовать SerDe JSON.

## <a name="next-steps"></a>Дополнительная информация

Другие статьи по этой теме см. в следующих источниках:

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла log4j Apache](../hdinsight-use-hive.md)
* [Анализ данных о задержке рейсов с помощью Hadoop в HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Анализ данных Twitter с помощью Hive в HDInsight](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

