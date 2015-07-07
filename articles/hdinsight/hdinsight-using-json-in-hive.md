<properties
   pageTitle="Анализ и обработка документов JSON с помощью Hive в HDInsight | Microsoft Azure"
   description="Узнайте, как использовать документы JSON и анализировать их с помощью Hive в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Узнайте, как обрабатывать и анализировать документы JSON в Hive

JSON — один из самых распространенных форматов в Интернете. Этот учебник поможет вам разобраться с одним из наиболее частых вопросов, которые возникают у пользователей Hive: как использовать документы JSON в Hive и анализировать их.

##Пример JSON

Рассмотрим пример. Предположим, что у нас есть документ JSON, показанный ниже. Мы хотим проанализировать его, а затем выполнять запросы, например искать значение по ключу, агрегату и т. д.

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

##Преобразование документа JSON в плоскую структуру (шаг требуется только в том случае, если используется сложная структура JSON)

Прежде чем использовать любой оператор Hive для анализа, необходимо предварительно обработать документ JSON и подготовить его к использованию в Hive.

Все параметры, перечисленные в следующем разделе, предполагают, что документ JSON содержит одну строку. Другими словами, нужно преобразовать документ JSON в плоскую структуру (строку), чтобы его могли использовать операторы Hive.

**Если документ JSON уже преобразован и представляет собой одну строку, вы можете пропустить этот шаг и сразу перейти к следующему разделу, посвященному анализу данных JSON.**

Предположим, что в хранилище больших двоичных объектов Azure в контейнере по умолчанию папки */json/ввода/* находится документ JSON с неплоской структурой, включающий несколько строк . Приведенный ниже код создает таблицу jsonexample, которая указывает на исходный документ JSON с неплоской структурой.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

Далее нам необходимо создать таблицу с именем one_line_json, которая будет указывать на плоский документ JSON и находиться в хранилище больших двоичных объектов Azure в контейнере по умолчанию папки */json/temp/*.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

После этого мы заполним эту таблицу плоскими данными JSON.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

Если сейчас взглянуть на только что созданную таблицу one_line_json, вы увидите, что она содержит документ JSON, представляющий собой одну строку

    select * from one_line_json;

Ниже приведены результаты этого запроса:

![Преобразование документа JSON в плоскую структуру][image-hdi-hivejson-flatten]

##Параметры для анализа документов JSON в Hive

Теперь у нас есть документ JSON в таблице с одним столбцом, и мы готовы выполнять запросы к этим данным с помощью Hive. Hive предоставляет три различных механизма для выполнения запросов к документам JSON:

1.	с помощью пользовательской функции (UDF) get_json_object;
2.	с помощью пользовательской функции json_tuple;
3.	с помощью пользовательского SerDe.

Рассмотрим каждый из них более подробно.

##Пользовательская функция get_json_object
Hive предоставляет встроенную пользовательскую функцию [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), которая поддерживает запросы JSON во время выполнения. Этот метод принимает два аргумента — имя таблицы, которая содержит плоский документ JSON, и поле JSON, которое требуется проанализировать. Рассмотрим на примере, как работает эта пользовательская функция.

Получение имени и фамилии каждого студента

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

Вот какие результаты дает выполнение этого запроса в окне консоли.

![Пользовательская функция get_json_object][image-hdi-hivejson-getjsonobject]

У этой пользовательской функции есть несколько ограничений.


- Одно из основных заключается в том, что функция не является высоко производительной, так как каждое поле в запросе требует повторного анализа запроса, что влияет на производительность.
- Во-вторых, get_json_object() возвращает строковое представление массива. Поэтому для преобразования результата в массив Hive необходимо использовать регулярные выражения, чтобы заменить квадратные скобки [ и ], а затем вызвать операцию split для получения массива.


Поэтому на вики-сайте Hive рекомендуется использовать функцию json_tuple, которую мы рассмотрим далее.


##Пользовательская функция json_tuple

Пользовательская функция, предоставляемая Hive, называется [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple). Она обеспечивает более высокую производительность, чем [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Этот метод принимает набор ключей и строку JSON, а затем возвращает кортеж из значений, используя одну функцию. Рассмотрим на примере, как работает эта пользовательская функция.

Получим идентификатор студента и его оценку из документа JSON

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

Мы используем синтаксис [LATERAL VIEW](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) в Hive, который позволяет json_tuple создать виртуальную таблицу путем применения пользовательской функции к каждой строке исходной таблицы.

Посмотрим на выходные данные этого сценария в консоли Hive:

![Пользовательская функция json_tuple][image-hdi-hivejson-jsontuple]

Одним из главных недостатков этой пользовательской функции является то, что со сложными документами JSON становится трудно работать из-за повторного использования LATERAL VIEW. На самом деле, эта пользовательская функция не может обрабатывать вложенные JSON.

##Пользовательский SerDe

Для анализа вложенных документов JSON **оптимальным выбором** является интерфейс SerDe, так как при этом можно определить схему JSON, что упрощает выполнение запросов.

Мы рассмотрим, как использовать один из популярных интерфейсов SerDe, который был разработан [rcongiu](https://github.com/rcongiu).

Шаг 1: убедитесь в наличии [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) (примечание: если установлен JDK 1.8, он не будет работать с этим SerDe).


- Если вы собираетесь использовать развертывание HDInsight в Windows, выберите версию JDK для 64-разрядной Windows.
- После завершения установки перейдите к панели управления, выберите "Добавить переменные среды" и добавьте новую переменную среды JAVA_HOME, которая указывает на C:\\Program Files\\Java\\jdk1.7.0_55 или на другую папку, в которой установлен JDK. На следующем снимке экрана показано, как задать переменную среды.

![Настройка правильных значений конфигурации для JDK][image-hdi-hivejson-jdk]

Шаг 2: перейдите по [этой](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) ссылке и скачайте Maven 3.3.1. Распакуйте архив туда, где вы хотите хранить двоичные файлы. В нашем случае это будет C:\\Program Files\\Maven. Добавьте в путь папку bin, выбрав на панели управления команду "Изменение системных переменных среды" для переменных среды вашей учетной записи. На следующем снимке экрана показано, как это сделать.

![Настройка Maven][image-hdi-hivejson-maven]

Шаг 3: склонируйте проект из сайта github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Это можно сделать, нажав кнопку Download Zip ("Скачать ZIP-файл"), как показано на следующем снимке экрана.

![Клонирование проекта][image-hdi-hivejson-serde]

Шаг 4: перейдите в папку, куда вы скачали пакет, и введите "mvn package". При этом должны создаться нужные JAR-файлы, который затем можно скопировать в кластер.

Шаг 5: перейдите в целевую папку в корневой папке, куда вы скачали пакет. Добавьте файл json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar в головной узел кластера. Я обычно помещаю его в папку двоичных файлов Hive: C:\\apps\\dist\\hive-0.13.0.2.1.11.0-2316\\bin или что-то подобное.
 
Шаг 6: в командной строке Hive введите "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Так как в моем случае JAR-файл находится в папке C:\\apps\\dist\\hive-0.13.x\\bin, можно напрямую добавить JAR с этим именем, как показано ниже:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![Добавление JAR в проект][image-hdi-hivejson-addjar]

Теперь мы можем использовать SerDe для выполнения запросов к документу JSON.

Сначала создадим таблицу со схемой документа JSON. Обратите внимание на то, что можно обрабатывать гораздо более сложные типы, включая сопоставления, массивы и структуры. Введите приведенный ниже код в консоли Hive, чтобы Hive создал таблицу json_table, которая может читать документ JSON, основываясь на схеме ниже.

    drop table json_table;
    create external table json_table (
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
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

Определив схему, выполним пару примеров, чтобы увидеть, как можно отправлять запросы к документу JSON с помощью Hive:

а) Вывод имени и фамилии учащегося

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Вот результат из консоли Hive:

![Запрос SerDe 1][image-hdi-hivejson-serde_query1]

б) Вычисление суммы оценок в документе JSON

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
В приведенном выше запросе используется пользовательская функция [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView), позволяющая развернуть массив оценок, чтобы просуммировать их.

Вот результат из консоли Hive:

![Запрос SerDe 2][image-hdi-hivejson-serde_query2]

c) Поиск предметов, по которым данный студент получил более 80 баллов: select jt.StudentClassCollection.ClassId from json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
Приведенный выше запрос возвращает массив Hive (в отличие от функции get_json_object, которая возвращает строку).

![Запрос SerDe 3][image-hdi-hivejson-serde_query3]

Если вы хотите пропустить документы JSON неправильного формата, просмотрите инструкции на [вики-странице](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) этого SerDe. Чтобы сделать это, можно ввести следующий код:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##Другие варианты
Кроме параметров, перечисленных ниже, можно также использовать собственный код, написанный на Python или другом языке, подходящем для вашей ситуации. Когда ваш сценарий Python будет готов, выполните его на Hive, как описано [в этой статье][hdinsight-python].

##Сводка
Напоследок следует заметить, что тип оператора JSON в Hive, который нужно выбрать, зависит от ситуации. Если вам требуется найти всего одно поле в простом документе JSON, можно применять пользовательскую функцию Hive get_json_object. Если нужно выполнить поиск по нескольким ключам, можно использовать json_tuple. При наличии вложенного документа следует использовать SerDe JSON.

Команда HDInsight работает над тем, чтобы еще больше упростить использование других форматов в Hive без значительных трудозатрат с вашей стороны. Мы обновим этот учебник более подробными сведениями, когда у нас будет чем поделиться.

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
 

<!---HONumber=62-->