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
ms.date: 04/26/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 034b61fdeb34c03bb4b58145f04138410e0d6fc6
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
# Обрабатывайте и анализируйте документы JSON с использованием Hive в HDInsight
<a id="process-and-analyze-json-documents-using-hive-in-hdinsight" class="xliff"></a>

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

Файл можно найти в wasbs://processjson@hditutorialdata.blob.core.windows.net/. Дополнительные сведения об использовании хранилища Azure Blob с HDInsight приведены в разделе [Использование HDFS-совместимой службы хранилища с Hadoop в HDInsight](hdinsight-hadoop-use-blob-storage.md). Вы можете скопировать файл в контейнер по умолчанию кластера.

В этом руководстве используется консоль Hive.  Инструкции по открытию консоли Hive приведены в разделе [Использование Hive с Hadoop в HDInsight с помощью удаленного рабочего стола](hdinsight-hadoop-use-hive-remote-desktop.md).

## Документы JSON, преобразованные в плоскую структуру
<a id="flatten-json-documents" class="xliff"></a>
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

Необработанный файл JSON находится в **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. Таблица Hive *StudentsRaw* указывает на необработанный документ JSON.

Таблица Hive *StudentsOneLine* сохраняет данные в файловой системе по умолчанию HDInsight в каталоге */json/students/*.

Запрос INSERT заполняет таблицу StudentOneLine плоскими данными JSON.

Запрос SELECT должен возвращать всего одну строку.

Вот результат выполнения запроса SELECT:

![Преобразование документа JSON в плоскую структуру][image-hdi-hivejson-flatten]

## Анализ документов JSON в Hive
<a id="analyze-json-documents-in-hive" class="xliff"></a>
Hive предоставляет три различных механизма для выполнения запросов к документам JSON:

* определяемая пользователем функция GET\_JSON\_OBJECT;
* определяемая пользователем функция JSON_TUPLE
* пользовательские SerDe
* написание собственной определяемой пользователем функции с использованием Python или других языков. Сведения о выполнении кода Python в Hive приведена [в этой статье][hdinsight-python].

### Использование определяемой пользователем функции GET\_JSON_OBJECT
<a id="use-the-getjsonobject-udf" class="xliff"></a>
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

### Использование определяемой пользователем функции JSON_TUPLE
<a id="use-the-jsontuple-udf" class="xliff"></a>
Еще одна определяемая пользователем функция в Hive называется [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), и она дает лучшие результаты по сравнению с [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Этот метод принимает набор ключей и строку JSON, а затем возвращает кортеж из значений, используя одну функцию. Следующий запрос возвращает идентификатор студента и его оценку из документа JSON.

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Выходные данные этого сценария в консоли Hive:

![Пользовательская функция json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE использует синтаксис [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) Hive, который позволяет json\_tuple создать виртуальную таблицу, применяя определяемую пользователем функцию к каждой строке исходной таблицы.  Из-за многократного использования LATERAL VIEW синтаксис сложных документов JSON становится слишком громоздким. Кроме того, JSON_TUPLE не может обрабатывать вложенные JSON.

### Использование пользовательских SerDe
<a id="use-custom-serde" class="xliff"></a>
SerDe — лучший выбор для синтаксического анализа вложенных документов JSON, так как с ними можно определить схему JSON и использовать ее для синтаксического анализа документов. В этом руководстве мы воспользуемся более распространенными SerDe, которые были разработаны [Роберто Конгиу](https://github.com/rcongiu) (Roberto Congiu).

**Для использования пользовательских SerDe выполните следующие действия:**

1. Установите [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Если вы собираетесь использовать развертывание HDInsight в Windows, выберите версию JDK для 64-разрядной Windows
   
   > [!WARNING]
   > JDK 1.8 не будет работать с этим SerDe.
   > 
   > 
   
    После завершения установки добавьте новую переменную среды пользователя:
   
   1. Откройте окно **Дополнительные параметры системы** в Windows.
   2. Выберите **Переменные среды**.  
   3. Добавьте новую переменную среды **JAVA_HOME**, которая указывает на **C:\Program Files\Java\jdk1.7.0_55** или на другую папку, в которую установлен JDK.
      
      ![Настройка правильных значений конфигурации для JDK][image-hdi-hivejson-jdk]
2. Установите [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)
   
    Добавьте в путь папку bin, выбрав на панели управления команду "Изменение системных переменных среды" для переменных среды вашей учетной записи. На следующем снимке экрана показано, как это сделать.
   
    ![Настройка Maven][image-hdi-hivejson-maven]
3. Создайте клон проекта с сайта [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) . Это можно сделать, нажав кнопку Download Zip ("Скачать ZIP-файл"), как показано на следующем снимке экрана.
   
    ![Клонирование проекта][image-hdi-hivejson-serde]

4: Перейдите в папку, куда скачан этот пакет, и введите mvn package. При этом должны создаться нужные JAR-файлы, который затем можно скопировать в кластер.

5: Перейдите в целевую папку в корневой папке, в которую загрузили пакет. Добавьте файл json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar в головной узел кластера. Я обычно помещаю его в папку двоичных файлов Hive: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin или что-то подобное.

6: В командной строке Hive введите “add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar”. Так как в моем случае JAR-файл находится в папке C:\apps\dist\hive-0.13.x\bin, можно напрямую добавить JAR с этим именем, как показано:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

   ![Добавление JAR в проект][image-hdi-hivejson-addjar]

Теперь мы можем использовать SerDe для выполнения запросов к документу JSON.

Следующий запрос создает таблицу с заданной схемой:

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Для вывода имени и фамилии студента

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Вот результат из консоли Hive:

![Запрос SerDe 1][image-hdi-hivejson-serde_query1]

Для вычисления суммы оценок из документа JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

В предыдущем запросе используется пользовательская функция [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView), позволяющая развернуть массив оценок, чтобы просуммировать их.

Вот результат из консоли Hive:

![Запрос SerDe 2][image-hdi-hivejson-serde_query2]

Для поиска предметов, по которым данный студент получил более 80 баллов:

    SELECT  
      jt.StudentClassCollection.ClassId
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;

Предыдущий запрос возвращает массив Hive (в отличие от функции get\_json\_object, возвращающей строку).

![Запрос SerDe 3][image-hdi-hivejson-serde_query3]

Если вы хотите пропустить документы JSON неправильного формата, просмотрите инструкции на [вики-странице](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) этого SerDe. Для этого можно ввести следующий код:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




## Сводка
<a id="summary" class="xliff"></a>
Напоследок следует заметить, что тип оператора JSON в Hive, который нужно выбрать, зависит от ситуации. Если вам требуется найти всего одно поле в простом документе JSON, можно применять пользовательскую функцию Hive get\_json\_object. Если нужно выполнить поиск по нескольким ключам, можно использовать json_tuple. При наличии вложенного документа следует использовать SerDe JSON.

## Дальнейшие действия
<a id="next-steps" class="xliff"></a>

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

