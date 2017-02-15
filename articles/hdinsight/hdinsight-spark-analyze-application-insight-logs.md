---
title: "Анализ журналов Application Insights с помощью Spark в HDInsight | Документация Майкрософт"
description: "Узнайте, как экспортировать журналы Application Insights в хранилище BLOB-объектов, а затем проанализировать их с помощью Spark в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dcb28a67ef21728d9b21159f356ed122e0b7a1be


---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Анализ журналов телеметрии Application Insights с помощью Spark в HDInsight
[Visual Studio Application Insights](../application-insights/app-insights-overview.md) — это служба аналитики, которая отслеживает ваши веб-приложения. Данные телеметрии, созданные службой Application Insights, можно экспортировать в службу хранилища Azure и там проанализировать их с помощью HDInsight.

В этом документе описывается, как использовать HDInsight для анализа данных телеметрии Application Insights с помощью Apache Spark.

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure.
* Приложение, настроенное для использования службы Application Insights. 
* Умение создавать кластеры HDInsight под управлением Linux. Дополнительные сведения о создании кластеров, если вы не знакомы с этим процессом, см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).
  
  > [!NOTE]
  > Этот документ не содержит пошаговых инструкций по созданию нового кластера. Но он ссылается на другие документы, содержащие сведения о создании кластера, который может обращаться к данным телеметрии.
  > 
  > 
* Веб-браузер. Используется для интерактивного выполнения анализа с применением записной книжки Jupyter.

При разработке и тестировании этого документа использовались следующие компоненты.

* Данные телеметрии Application Insights были созданы с помощью [веб-приложения Node.js, настроенного для использования Application Insights](../application-insights/app-insights-nodejs.md).
* Для анализа данных использована платформа Spark под управлением Linux в кластере HDInsight версии 3.4.

## <a name="architecture-and-planning"></a>Архитектура и планирование
На следующей схеме показана архитектура служб данного примера:

![Схема, показывающая поток данных из Application Insights в хранилище BLOB-объектов с последующей обработкой Spark в HDInsight](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Хранилище Azure
Кластер HDInsight может непосредственно обращаться к блочным BLOB-объектам из учетной записи хранения Azure, а в службе Application Insights можно настроить непрерывный экспорт данных телеметрии в большие двоичные объекты в хранилище Azure. Однако существует ряд требований, которые необходимо выполнить. Они описаны ниже.

* **Расположение.** Учетная запись хранения должна находиться в том же регионе, что и HDInsight. Это позволяет сократить задержку при доступе к данным и избежать расходов на исходящий трафик, который возникает при переносе данных между регионами.
* **Тип большого двоичного объекта.** HDInsight поддерживает только блочные BLOB-объекты. Служба Application Insights по умолчанию использует блочные BLOB-объекты, поэтому должна работать с HDInsight.
* **Разрешения на доступ.** Если для непрерывного экспорта в службе Application Insights и для хранилища по умолчанию HDInsight используется одна учетная запись хранения, то HDInsight имеет полный доступ к данным телеметрии Application Insights. Это означает, что данные телеметрии можно удалить из кластера HDInsight.
  
    Вместо этого для телеметрии Application Insights и HDInsight рекомендуется использовать отдельные учетные записи хранения, а также [использовать подписанные URL-адреса (SAS) для ограничения доступа к данным из HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md). Применение SAS позволяет предоставлять HDInsight доступ к данным телеметрии только для чтения.

### <a name="data-schema"></a>Схема данных
Application Insights предоставляет сведения об [экспорте модели данных](../application-insights/app-insights-export-data-model.md) для формата данных телеметрии, экспортируемых в большие двоичные объекты. В этом документе для работы с данными используется Spark SQL. Spark SQL может автоматически создавать схему для структуры данных JSON, записываемых Application Insights, поэтому вам не нужно определять схему вручную при выполнении анализа.

## <a name="export-telemetry-data"></a>Экспорт данных телеметрии
Чтобы настроить непрерывный экспорт данных телеметрии из Application Insights в большой двоичный объект хранилища Azure, выполните действия, описанные в статье [Экспорт данных телеметрии из Application Insights](../application-insights/app-insights-export-telemetry.md) .

## <a name="configure-hdinsight-to-access-the-data"></a>Настройка доступа к данным из HDInsight
Сведения о настройке SAS-доступа к контейнеру больших двоичных объектов, содержащему экспортированные данные телеметрии, см. в статье [Использование подписанных URL-адресов хранилища Azure для ограничения доступа к данным с помощью HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md). Использование SAS позволит предоставить доступ только для чтения данных.

В статье, посвященной подписанным URL-адресам, содержатся сведения о добавлении хранилища SAS в существующий кластер HDInsight под управлением Linux. В ней также содержатся сведения о том, как добавить такое хранилище при создании кластера HDInsight.

## <a name="analyze-the-data-using-python-pyspark"></a>Анализ данных с помощью Python (PySpark)
1. На [портале Azure](https://portal.azure.com)выберите кластер Spark в HDInsight. В разделе **Быстрые ссылки** выберите **Панели мониторинга кластера**, а затем в колонке "Панель мониторинга кластера" выберите **Записная книжка Jupyter**.
   
    ![Панели мониторинга кластера](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. В правом верхнем углу страницы Jupyter щелкните **Создать**, а затем выберите **PySpark**. При этом откроется новая вкладка браузера, содержащая записную книжку Jupyter на основе Python.
3. На этой странице в первом поле (которое называется **ячейка**) введите следующую команду.
   
        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   
    Это позволит Spark рекурсивно обращаться к структуре каталогов для получения входных данных. Телеметрия Application Insights записывается в структуру каталогов, аналогичную следующей:
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. Для выполнения кода используйте сочетание клавиш **SHIFT+ВВОД**. Слева от ячейки в скобках отобразится звездочка (\*), показывая тем самым, что код в этой ячейке выполняется. После завершения выполнения звездочку (\*) заменит число, а под ячейкой отобразятся выходные данные, аналогичные следующим:
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Под первой ячейкой будет создана новая ячейка. Введите в новую ячейку следующие данные. Замените **STORAGEACCOUNT** и **CONTAINER** именами учетной записи хранения Azure и контейнера больших двоичных объектов, которые вы использовали при настройке непрерывного экспорта в службе Application Insights.
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    Для выполнения этой ячейки используйте сочетание клавиш **SHIFT+ВВОД**. Отобразится результат, аналогичный следующему:
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    Полученный путь WASB является расположением данных телеметрии Application Insights. Замените строку `hdfs dfs -ls` в ячейке на полученный путь WASB, а затем используйте сочетание клавиш **SHIFT+ВВОД** для повторного выполнения ячейки. На этот раз в результате выполнения должны отобразиться каталоги, содержащие данные телеметрии.
   
   > [!NOTE]
   > Для остальных действий, описанных в этом разделе, использовался каталог `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Этот каталог может отсутствовать, если ваши данные телеметрии не предназначены для веб-приложения. При использовании данных телеметрии, которые не включают каталог запросов, выберите другой каталог и отредактируйте остальные шаги таким образом, чтобы в них использовались этот каталог и схема данных, сохраненных в нем.
   > 
   > 
6. В следующей ячейке введите приведенные ниже данные. Вместо **WASB\_PATH** укажите путь из предыдущего шага.
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    При этом создается новая таблица данных на основе файлов JSON, экспортируемых в процессе непрерывного экспорта. Для выполнения ячейки используйте сочетание клавиш **SHIFT+ВВОД**.
7. В следующей ячейке введите и выполните такую команду, чтобы просмотреть схему, созданную Spark для JSON-файлов:
   
        jsonData.printSchema()
   
    Схемы для разных типов телеметрии будут отличаться. Ниже приведена схема, созданная для веб-запросов (для данных, хранящихся в подкаталоге `Requests` ):
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Чтобы зарегистрировать таблицу данных в качестве временной таблицы и выполнить запрос на основе этих данных, используйте следующую команду:
   
        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   
    Этот запрос вернет сведения о городе для первых 20 записей, где параметр context.location.city не равен нулю (NOT NULL).
   
   > [!NOTE]
   > Структура контекста присутствует во всех данных телеметрии, записываемых Application Insights. Однако элемент city (город) в журналах может не заполняться. Используйте эту схему для определения других элементов, которые можно запросить и которые могут содержать данные для журналов.
   > 
   > 
   
    Этот запрос возвращает сведения, аналогичные следующим:
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-using-scala"></a>Анализ данных с помощью Scala
1. На [портале Azure](https://portal.azure.com)выберите кластер Spark в HDInsight. В разделе **Быстрые ссылки** выберите **Панели мониторинга кластера**, а затем в колонке "Панель мониторинга кластера" выберите **Записная книжка Jupyter**.
   
    ![Панели мониторинга кластера](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. В правом верхнем углу страницы Jupyter щелкните **Создать**, а затем выберите **Scala**. При этом откроется новая вкладка браузера, содержащая записную книжку Jupyter на основе Scala.
3. На этой странице в первом поле (которое называется **ячейка**) введите следующую команду.
   
        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   
    Это позволит Spark рекурсивно обращаться к структуре каталогов для получения входных данных. Телеметрия Application Insights записывается в структуру каталогов, аналогичную следующей:
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. Для выполнения кода используйте сочетание клавиш **SHIFT+ВВОД**. Слева от ячейки в скобках отобразится звездочка (\*), показывая тем самым, что код в этой ячейке выполняется. После завершения выполнения звездочку (\*) заменит число, а под ячейкой отобразятся выходные данные, аналогичные следующим:
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Под первой ячейкой будет создана новая ячейка. Введите в новую ячейку следующие данные. Замените **STORAGEACCOUNT** и **CONTAINER** именами учетной записи хранения Azure и контейнера больших двоичных объектов, которые вы использовали при настройке непрерывного экспорта в службе Application Insights.
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    Для выполнения этой ячейки используйте сочетание клавиш **SHIFT+ВВОД**. Отобразится результат, аналогичный следующему:
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    Полученный путь WASB является расположением данных телеметрии Application Insights. Замените строку `hdfs dfs -ls` в ячейке на полученный путь WASB, а затем используйте сочетание клавиш **SHIFT+ВВОД** для повторного выполнения ячейки. На этот раз в результате выполнения должны отобразиться каталоги, содержащие данные телеметрии.
   
   > [!NOTE]
   > Для остальных действий, описанных в этом разделе, использовался каталог `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Этот каталог может отсутствовать, если ваши данные телеметрии не предназначены для веб-приложения. При использовании данных телеметрии, которые не включают каталог запросов, выберите другой каталог и отредактируйте остальные шаги таким образом, чтобы в них использовались этот каталог и схема данных, сохраненных в нем.
   > 
   > 
6. В следующей ячейке введите приведенные ниже данные. Вместо **WASB\_PATH** укажите путь из предыдущего шага.
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    При этом создается новая таблица данных на основе файлов JSON, экспортируемых в процессе непрерывного экспорта. Для выполнения ячейки используйте сочетание клавиш **SHIFT+ВВОД**.
7. В следующей ячейке введите и выполните такую команду, чтобы просмотреть схему, созданную Spark для JSON-файлов:
   
        jsonData.printSchema
   
    Схемы для разных типов телеметрии будут отличаться. Ниже приведена схема, созданная для веб-запросов (для данных, хранящихся в подкаталоге `Requests` ):
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Чтобы зарегистрировать таблицу данных в качестве временной таблицы и выполнить запрос на основе этих данных, используйте следующую команду:
   
        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   
    Этот запрос вернет сведения о городе для первых 20 записей, где параметр context.location.city не равен нулю (NOT NULL).
   
   > [!NOTE]
   > Структура контекста присутствует во всех данных телеметрии, записываемых Application Insights. Однако элемент city (город) в журналах может не заполняться. Используйте эту схему для определения других элементов, которые можно запросить и которые могут содержать данные для журналов.
   > 
   > 
   
    Этот запрос возвращает сведения, аналогичные следующим:
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные примеры применения Spark при работе с данными и службами в Azure см. в следующих документах:

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Дополнительные сведения о создании и запуске приложений Spark см. в следующих документах:

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)




<!--HONumber=Nov16_HO3-->


