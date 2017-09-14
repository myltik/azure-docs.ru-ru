---
title: "Анализ и обработка документов JSON с помощью Hive в HDInsight | Документация Майкрософт"
description: "Узнайте, как использовать документы JSON и анализировать их с помощью Hive в HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: ee7d40d2ff0ae1ac10b54f4c1f1dd704a70eb70c
ms.contentlocale: ru-ru
ms.lasthandoff: 09/06/2017

---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Обрабатывайте и анализируйте документы JSON с использованием Hive в HDInsight

Узнайте, как обрабатывать и анализировать файлы JSON с помощью Hive в HDInsight. В этом руководстве используется следующий документ JSON:

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

Файл можно найти в wasb://processjson@hditutorialdata.blob.core.windows.net/. Дополнительные сведения об использовании хранилища Azure Blob с HDInsight приведены в разделе [Использование HDFS-совместимой службы хранилища с Hadoop в HDInsight](hdinsight-hadoop-use-blob-storage.md). Вы можете скопировать файл в контейнер по умолчанию кластера.

В этом руководстве используется консоль Hive.  Инструкции по открытию консоли Hive приведены в разделе [Использование Hive с Hadoop в HDInsight с помощью удаленного рабочего стола](hdinsight-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Документы JSON, преобразованные в плоскую структуру
Методы, перечисленные в следующем разделе требуют, чтобы документ JSON содержал одну строку. Поэтому необходимо преобразовать документ JSON в строку. Если документ JSON уже преобразован, можно пропустить этот шаг и сразу перейти к следующему разделу, посвященному анализу данных JSON.

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

Необработанный файл JSON находится в **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Таблица Hive *StudentsRaw* указывает на необработанный документ JSON.

Таблица Hive *StudentsOneLine* сохраняет данные в файловой системе по умолчанию HDInsight в каталоге */json/students/*.

Запрос INSERT заполняет таблицу StudentOneLine плоскими данными JSON.

Запрос SELECT должен возвращать всего одну строку.

Вот результат выполнения запроса SELECT:

![Преобразование документа JSON в плоскую структуру][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Анализ документов JSON в Hive
Hive предоставляет три различных механизма для выполнения запросов к документам JSON:

* определяемая пользователем функция GET\_JSON\_OBJECT;
* определяемая пользователем функция JSON_TUPLE
* пользовательские SerDe
* написание собственной определяемой пользователем функции с использованием Python или других языков. Сведения о выполнении кода Python в Hive приведена [в этой статье][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Использование определяемой пользователем функции GET\_JSON_OBJECT
В Hive есть встроенная определяемая пользователем функция под названием [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), которая может выполнять запросы JSON во время выполнения. Этот метод принимает два аргумента — имя таблицы, которая содержит плоский документ JSON, и поле JSON, которое требуется проанализировать. Рассмотрим на примере, как работает эта пользовательская функция.

Получение имени и фамилии каждого студента

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Вот какие результаты дает выполнение этого запроса в окне консоли.

![Пользовательская функция get_json_object][image-hdi-hivejson-getjsonobject]

У определяемой пользователем функции get-json_object есть несколько ограничений.

* Так как каждое поле в запросе требует повторного синтаксического анализа запроса, это влияет на производительность.
* GET\_JSON_OBJECT() возвращает строковое представление массива. Для преобразования этого массива в массив Hive необходимо использовать регулярные выражения для замены квадратных скобок "[" и "]", а затем также выполнить разбиение для получения массива.

Именно поэтому в вики Hive рекомендуется использовать json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Использование определяемой пользователем функции JSON_TUPLE
Еще одна определяемая пользователем функция в Hive называется [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), и она дает лучшие результаты по сравнению с [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Этот метод принимает набор ключей и строку JSON, а затем возвращает кортеж из значений, используя одну функцию. Следующий запрос возвращает идентификатор студента и его оценку из документа JSON.

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Выходные данные этого сценария в консоли Hive:

![Пользовательская функция json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE использует синтаксис [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) Hive, который позволяет json\_tuple создать виртуальную таблицу, применяя определяемую пользователем функцию к каждой строке исходной таблицы.  Из-за многократного использования LATERAL VIEW синтаксис сложных документов JSON становится слишком громоздким. Кроме того, JSON_TUPLE не может обрабатывать вложенные JSON.

### <a name="use-custom-serde"></a>Использование пользовательских SerDe
SerDe — лучший выбор для синтаксического анализа вложенных документов JSON, так как с ними можно определить схему JSON и использовать ее для синтаксического анализа документов. См. инструкции по [использованию пользовательского формата SerDe JSON с Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Сводка
Напоследок следует заметить, что тип оператора JSON в Hive, который нужно выбрать, зависит от ситуации. Если вам требуется найти всего одно поле в простом документе JSON, можно применять пользовательскую функцию Hive get\_json\_object. Если нужно выполнить поиск по нескольким ключам, можно использовать json_tuple. При наличии вложенного документа следует использовать SerDe JSON.

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи по этой теме приведены в

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла log4j Apache](hdinsight-use-hive.md)
* [Анализ данных о задержке рейсов с помощью Hadoop в HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Анализ данных Twitter с помощью Hive в HDInsight](hdinsight-analyze-twitter-data.md)
* [Run an Apache Hive, Pig, or Hadoop job using Azure Cosmos DB and HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md) (Выполнение задания Apache Hive, Pig или Hadoop с помощью Azure Cosmos DB и HDInsight)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

