---
title: Анализ журналов Application Insights с помощью Spark в Azure HDInsight | Документация Майкрософт
description: Узнайте, как экспортировать журналы Application Insights в хранилище BLOB-объектов, а затем проанализировать их с помощью Spark в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.openlocfilehash: 31068376e20b240a440432319e65f4e479163ee0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939606"
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Анализ журналов телеметрии Application Insights с помощью Spark в HDInsight

Сведения об анализе данных телеметрии Application Insight с помощью Spark в HDInsight.

[Visual Studio Application Insights](../../application-insights/app-insights-overview.md) — это служба аналитики, которая отслеживает ваши веб-приложения. Данные телеметрии, созданные службой Application Insights, можно экспортировать в службу хранилища Azure. Затем вы можете использовать HDInsight, чтобы проанализировать их.

## <a name="prerequisites"></a>предварительным требованиям

* Приложение, настроенное для использования службы Application Insights.

* Умение создавать кластеры HDInsight под управлением Linux. Дополнительные сведения см. в статье [Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Веб-браузер.

При разработке и тестировании этого документа использовались следующие ресурсы:

* Данные телеметрии Application Insights были созданы с помощью [веб-приложения Node.js, настроенного для использования Application Insights](../../application-insights/app-insights-nodejs.md).

* Для анализа данных использована платформа Spark под управлением Linux в кластере HDInsight версии 3.5.

## <a name="architecture-and-planning"></a>Архитектура и планирование

На следующей схеме показана архитектура служб данного примера:

![Схема, показывающая поток данных из Application Insights в хранилище BLOB-объектов с последующей обработкой Spark в HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Хранилище Azure

В службе Application Insights можно настроить непрерывный экспорт данных телеметрии в большие двоичные объекты. Затем HDInsight сможет считывать данные, хранимые в больших двоичных объектах. Однако существует ряд требований, которые необходимо выполнить. Они описаны ниже.

* **Расположение.** Если учетная запись хранения и HDInsight находятся в разных местах, это может увеличить задержку. Это также увеличивает затраты, так как передача данных между регионами оплачивается как исходящий трафик.

    > [!WARNING]
    > Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

* **Тип большого двоичного объекта.** HDInsight поддерживает только блочные BLOB-объекты. Служба Application Insights по умолчанию использует блочные BLOB-объекты, поэтому должна работать с HDInsight.

Сведения о добавлении хранилища в имеющийся кластер см. статье [Добавление дополнительных учетных записей хранения в HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="data-schema"></a>Схема данных

Application Insights предоставляет сведения об [экспорте модели данных](../../application-insights/app-insights-export-data-model.md) для формата данных телеметрии, экспортируемых в большие двоичные объекты. В этом документе для работы с данными используется Spark SQL. Spark SQL может автоматически создавать схему для структуры данных JSON, регистрируемой службой Application Insights.

## <a name="export-telemetry-data"></a>Экспорт данных телеметрии

Чтобы настроить непрерывный экспорт данных телеметрии из Application Insights в большой двоичный объект хранилища Azure, выполните действия, описанные в статье [Экспорт данных телеметрии из Application Insights](../../application-insights/app-insights-export-telemetry.md) .

## <a name="configure-hdinsight-to-access-the-data"></a>Настройка доступа к данным из HDInsight

Если вы создаете кластер HDInsight, прямо в процессе добавьте учетную запись хранения.

Чтобы добавить учетную запись хранения Azure в имеющийся кластер, ознакомьтесь со сведениями, приведенными в статье [Добавление дополнительных учетных записей хранения в HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="analyze-the-data-pyspark"></a>Анализ данных. PySpark

1. На [портале Azure](https://portal.azure.com)выберите кластер Spark в HDInsight. В разделе **Быстрые ссылки** выберите **Панели мониторинга кластера**, а затем в разделе "Панель мониторинга кластера" выберите **Записная книжка Jupyter**.

    ![Панели мониторинга кластера](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. В правом верхнем углу страницы Jupyter щелкните **Создать**, а затем выберите **PySpark**. При этом откроется новая вкладка браузера, содержащая записную книжку Jupyter на основе Python.

3. На этой странице в первом поле (которое называется **ячейка**) введите следующий текст:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Этот код настраивает Spark рекурсивно обращаться к структуре каталогов для получения входных данных. Телеметрия Application Insights записывается в структуру каталогов, аналогичную этой: `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Для выполнения кода используйте сочетание клавиш **SHIFT+ВВОД**. Слева от ячейки в скобках отобразится звездочка (\*), показывая тем самым, что код в этой ячейке выполняется. После завершения выполнения звездочку (\*) заменит число, а под ячейкой отобразятся данные, аналогичные следующим:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Под первой ячейкой будет создана ячейка. Введите в новую ячейку следующие данные. Замените `CONTAINER` и `STORAGEACCOUNT` именем учетной записи хранения Azure и именем контейнера больших двоичных объектов, который содержит данные службы Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Для выполнения этой ячейки используйте сочетание клавиш **SHIFT+ВВОД**. Отобразится результат, аналогичный следующему:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Полученный путь WASB является расположением данных телеметрии Application Insights. Замените строку `hdfs dfs -ls` в ячейке на полученный путь WASB, а затем используйте сочетание клавиш **SHIFT+ВВОД** для повторного выполнения ячейки. На этот раз в результате выполнения должны отобразиться каталоги, содержащие данные телеметрии.

   > [!NOTE]
   > Для остальных действий, описанных в этом разделе, использовался каталог `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Структура каталога может отличаться.

6. В следующей ячейке введите код ниже: замените `WASB_PATH` на путь, который вы использовали на предыдущем шаге.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Этот код создает кадр данных на основе файлов JSON, экспортируемых в процессе непрерывного экспорта. Для выполнения ячейки используйте сочетание клавиш **SHIFT+ВВОД**.
7. В следующей ячейке введите и выполните такую команду, чтобы просмотреть схему, созданную Spark для JSON-файлов:

   ```python
   jsonData.printSchema()
   ```

    Схемы для разных типов телеметрии будут отличаться. Ниже приведен пример схемы, созданной для веб-запросов (для данных, хранящихся в подкаталоге `Requests`):

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

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Этот запрос вернет сведения о городе для первых 20 записей, где параметр context.location.city не равен нулю (не имеет значение NULL).

   > [!NOTE]
   > Структура контекста присутствует во всех данных телеметрии, записываемых Application Insights. Однако элемент city (город) в журналах может не заполняться. Используйте эту схему для определения других элементов, которые можно запросить и которые могут содержать данные для журналов.

    Этот запрос возвращает следующие сведения:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Анализ данных. Scala

1. На [портале Azure](https://portal.azure.com)выберите кластер Spark в HDInsight. В разделе **Быстрые ссылки** выберите **Панели мониторинга кластера**, а затем в разделе "Панель мониторинга кластера" выберите **Записная книжка Jupyter**.

    ![Панели мониторинга кластера](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. В правом верхнем углу страницы Jupyter щелкните **Создать**, а затем выберите **Scala**. При этом откроется новая вкладка браузера, содержащая записную книжку Jupyter на основе Scala.
3. На этой странице в первом поле (которое называется **ячейка**) введите следующий текст:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Этот код настраивает Spark рекурсивно обращаться к структуре каталогов для получения входных данных. Телеметрия Application Insights записывается в структуру каталогов, аналогичную этой: `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Для выполнения кода используйте сочетание клавиш **SHIFT+ВВОД**. Слева от ячейки в скобках отобразится звездочка (\*), показывая тем самым, что код в этой ячейке выполняется. После завершения выполнения звездочку (\*) заменит число, а под ячейкой отобразятся данные, аналогичные следующим:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Под первой ячейкой будет создана ячейка. Введите в новую ячейку следующие данные. Замените `CONTAINER` и `STORAGEACCOUNT` именем учетной записи хранения Azure и именем контейнера больших двоичных объектов, который содержит журналы службы Application Insights.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Для выполнения этой ячейки используйте сочетание клавиш **SHIFT+ВВОД**. Отобразится результат, аналогичный следующему:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Полученный путь WASB является расположением данных телеметрии Application Insights. Замените строку `hdfs dfs -ls` в ячейке на полученный путь WASB, а затем используйте сочетание клавиш **SHIFT+ВВОД** для повторного выполнения ячейки. На этот раз в результате выполнения должны отобразиться каталоги, содержащие данные телеметрии.

   > [!NOTE]
   > Для остальных действий, описанных в этом разделе, использовался каталог `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Этот каталог может отсутствовать, если ваши данные телеметрии не предназначены для веб-приложения.

6. В следующей ячейке введите код ниже: замените `WASB\_PATH` на путь, который вы использовали на предыдущем шаге.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Этот код создает кадр данных на основе файлов JSON, экспортируемых в процессе непрерывного экспорта. Для выполнения ячейки используйте сочетание клавиш **SHIFT+ВВОД**.

7. В следующей ячейке введите и выполните такую команду, чтобы просмотреть схему, созданную Spark для JSON-файлов:

   ```scala
   jsonData.printSchema
   ```

    Схемы для разных типов телеметрии будут отличаться. Ниже приведен пример схемы, созданной для веб-запросов (для данных, хранящихся в подкаталоге `Requests`):

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

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Этот запрос вернет сведения о городе для первых 20 записей, где параметр context.location.city не равен нулю (не имеет значение NULL).

   > [!NOTE]
   > Структура контекста присутствует во всех данных телеметрии, записываемых Application Insights. Однако элемент city (город) в журналах может не заполняться. Используйте эту схему для определения других элементов, которые можно запросить и которые могут содержать данные для журналов.
   >
   >

    Этот запрос возвращает следующие сведения:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры применения Spark при работе с данными и службами в Azure см. в следующих документах:

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

Дополнительные сведения о создании и запуске приложений Spark см. в следующих документах:

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)
