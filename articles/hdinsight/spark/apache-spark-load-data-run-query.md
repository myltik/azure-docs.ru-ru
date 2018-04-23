---
title: Выполнение интерактивных запросов в кластере Azure HDInsight Spark | Документация Майкрософт
description: Краткое руководство HDInsight Spark по созданию кластера Apache Spark в HDInsight.
keywords: краткое руководство Spark,интерактивный запрос Spark,интерактивный запрос,Hdinsight Spark,Azure Spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 177fb47c72e9abbafcda69416643fbd3848373bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Выполнение интерактивных запросов в кластерах Spark в HDInsight

В этой статье описано, как с помощью записной книжки Jupyter выполнять интерактивные запросы Spark SQL к кластеру Spark. 

[Записная книжка Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) — это браузерное приложение, которое позволяет использовать интерактивный интерфейс на базе консоли в Интернете. Spark в HDInsight также содержит [записную книжку Zeppelin](apache-spark-zeppelin-notebook.md). В этом руководстве используется записная книжка Jupyter.

Записные книжки Jupyter в кластерах HDInsight поддерживают три версии ядер: **PySpark**, **PySpark3** и **Spark**. В этом руководстве используется ядро **PySpark**. Дополнительные сведения об этих ядрах и о преимуществах **PySpark** см. в статье [Ядра для записной книжки Jupyter в кластерах Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md). См. дополнительные сведения об [использовании записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](./apache-spark-zeppelin-notebook.md).

Из этого руководства вы узнаете как обращаться к данным в CSV-файле. Сначала в Spark необходимо загрузить данные в виде кадра данных. Затем к кадру данных можно выполнять запросы с помощью записной книжки Jupyter. 

## <a name="prerequisites"></a>предварительным требованиям

* **Кластер Azure HDInsight Spark**. Инструкции см. в статье [Создание кластера Apache Spark в Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Работа с записной книжкой Jupyter при использовании ядра PySpark**. Инструкции см. в разделе по [созданию записной книжки Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Создание кадра данных из CSV-файла

С помощью класса SQLContext приложения могут создавать кадры данных из существующего набора данных RDD, а также из таблицы Hive или других источников данных. 

**Чтобы создать кадр данных из CSV-файла:**

1. Если у вас нет записной книжки Jupyter, создайте ее с помощью PySpark. Инструкции см. в разделе по [созданию записной книжки Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Вставьте следующий код в пустую ячейку приложения и нажмите **SHIFT+ВВОД** для выполнения кода. Код импортирует типы, необходимые для этого сценария:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Если приложение создается с использованием ядра PySpark, при выполнении первой ячейки кода контексты Spark и Hive будут созданы автоматически. Вам не нужно явно создавать контексты.

    При запуске интерактивного запроса в Jupyter в заголовке окна веб-браузера или вкладки будет отображаться состояние **(Busy)** (Занято), а также название приложения. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания он изменится на кружок без заливки.

    ![Состояние интерактивного запроса Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Состояние интерактивного запроса Spark SQL")

3. Выполните следующий код, чтобы создать кадр данных и временную таблицу (**hvac**). Код извлекает не все колонки, доступные в CSV-файле. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    На снимке экрана показан моментальный снимок файла hvac.csv. CSV-файл содержит все кластеры HDInsigt Spark. Эти данные демонстрируют колебания температуры в здании.

    ![Моментальный снимок данных для интерактивных запросов Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snapshot of data for interactive Spark SQL query")

## <a name="run-queries-on-the-dataframe"></a>Выполнение запросов к кадру данных

Когда таблица будет готова, выполните интерактивный запрос к данным.

**Чтобы выполнить запрос:**

1. В пустой ячейке приложения выполните следующий код:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Так как для работы приложения используется ядро PySpark, вы можете отправить интерактивный SQL-запрос непосредственно к временной таблице **hvac**, которую вы создали с помощью волшебной команды `%%sql`. Дополнительные сведения о магической команде `%%sql`, а также других магических командах, доступных в ядре PySpark, приведены в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   По умолчанию выводятся следующие табличные данные.

     ![Таблица результатов интерактивного запроса Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Таблица результатов интерактивного запроса Spark")

3. Результаты также можно просмотреть и в других визуализациях. Чтобы увидеть результат в виде диаграммы с областями, выберите **Область** и укажите другие значения, как показано ниже.

    ![Диаграмма с областями результата интерактивного запроса Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Диаграмма с областями результата интерактивного запроса Spark")

10. Для этого в меню **Файл** записной книжки выберите пункт **Save and Checkpoint** (Сохранить и установить контрольную точку). 

11. Если вы собираетесь ознакомиться со [следующим руководством](apache-spark-use-bi-tools.md) сейчас, не закрывайте записную книжку. В противном случае завершите работу записной книжки, чтобы освободить ресурсы кластера: в меню **Файл** записной книжки щелкните **Close and Halt** (Закрыть и остановить).

## <a name="next-step"></a>Дальнейшие действия

Из этой статьи вы узнали, как выполнять интерактивные запросы в Spark с помощью записной книжки Jupyter. Теперь переходите к следующей статье, в которой объясняется, как перенести зарегистрированные в Spark данные в средство бизнес-аналитики, например в Power BI или Tableau. 

> [!div class="nextstepaction"]
>[Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight](apache-spark-use-bi-tools.md)




