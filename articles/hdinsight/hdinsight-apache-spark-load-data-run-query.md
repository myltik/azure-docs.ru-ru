---
title: "Выполнение интерактивных запросов в кластере Azure HDInsight Spark | Документация Майкрософт"
description: "Краткое руководство HDInsight Spark по созданию кластера Apache Spark в HDInsight."
keywords: "краткое руководство Spark,интерактивный запрос Spark,интерактивный запрос,Hdinsight Spark,Azure Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: 7853988f91372f2ab563481e441c0d6db4a7fd96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Выполнение интерактивных запросов в кластере HDInsight Spark

В этой статье описано, как с помощью записной книжки Jupyter выполнять интерактивные запросы Spark SQL к кластеру Spark. Записная книжка Jupyter представляет собой браузерное приложение, которое позволяет использовать интерактивный интерфейс на базе консоли в Интернете. Дополнительные сведения см. в [документации по записным книжкам Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

В ходе работы с этим руководством вы выполните интерактивный SQL-запрос к Spark с помощью ядра **PySpark** в записной книжке Jupyter. Записные книжки Jupyter в кластерах HDInsight поддерживают еще две версии ядер — **PySpark3** и **Spark**. Дополнительные сведения об этих ядрах и о преимуществах **PySpark** см. в статье [Ядра для записной книжки Jupyter в кластерах Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Предварительные требования

* **Кластер Azure HDInsight Spark**. Инструкции см. в статье [Создание кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Создание записной книжки Jupyter для выполнения интерактивных запросов

Чтобы выполнять запросы, мы воспользуемся тестовым набором данных, который по умолчанию всегда доступен в хранилище, связанном с кластером. Но эти данные нужно сначала загрузить в Spark в виде кадра данных. Когда кадр данных будет готов, вы сможете отправлять запросы к нему с помощью записной книжки Jupyter. Из этого раздела вы узнаете, как выполнять следующие действия:

* регистрация тестового набора данных в качестве кадра данных Spark;
* выполнение запросов к кадру данных.

1. Откройте [портал Azure](https://portal.azure.com/). Если закрепили кластер на панели мониторинга, щелкните элемент кластера на панели мониторинга, чтобы открыть колонку кластера.

    Если вы не закрепили кластер на панели мониторинга, в области слева щелкните **Кластеры HDInsight**, а затем выберите созданный кластер.

3. В разделе **Быстрые ссылки** щелкните **Панели мониторинга кластера**, а затем — **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

   ![Открытие записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Открытие записной книжки Jupyter для выполнения интерактивного запроса Spark SQL")

   > [!NOTE]
   > Также можно открыть записную книжку Jupyter для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Создайте записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

   ![Создание записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Создание записной книжки Jupyter для выполнения интерактивного запроса Spark SQL")

   Будет создана и открыта записная книжка с именем Untitled (Untitled.pynb).

4. Щелкните имя записной книжки вверху и по желанию введите понятное имя.

    ![Указание имени записной книжки Jupter, из которой будет выполняться интерактивный запрос Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Указание имени записной книжки Jupter, из которой будет выполняться интерактивный запрос Spark")

5. Вставьте указанный ниже код в пустую ячейку и нажмите сочетание клавиш **SHIFT + ВВОД**, чтобы выполнить код. Код импортирует типы, необходимые для этого сценария:

        from pyspark.sql import *
        from pyspark.sql.types import *

    Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода.

    ![Состояние интерактивного запроса Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Состояние интерактивного запроса Spark SQL")

    При каждом запуске интерактивного запроса в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания он изменится на кружок без заливки.

6. Прежде чем загружать данные в кластер Spark, давайте немного изучим их. Тестовые данные, используемые в этом руководстве, доступны на всех кластерах HDInsight Spark в виде CSV-файла по адресу **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Эти данные демонстрируют колебания температуры в здании. Ниже вы видите несколько первых строк из этого набора данных.

    ![Моментальный снимок данных для интерактивных запросов Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snapshot of data for interactive Spark SQL query")

6. Создайте кадр данных и временную таблицу **hvac**, выполнив следующий код: Для целей этого руководства нам не нужно создавать во временной таблице все столбцы, присутствующие в необработанных данных в формате CSV. 

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

7. Когда таблица будет готова, выполните интерактивный запрос к данным с помощью следующего кода.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Так как вы используете ядро PySpark, вы можете отправить интерактивный SQL-запрос непосредственно к временной таблице **hvac**, которую вы создали с помощью волшебной команды `%%sql`. Дополнительные сведения о магической команде `%%sql`, а также других магических командах, доступных в ядре PySpark, приведены в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   По умолчанию выводятся следующие табличные данные.

     ![Таблица результатов интерактивного запроса Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Таблица результатов интерактивного запроса Spark")

    Результаты также можно просмотреть и в других визуализациях. Например, диаграмма областей для тех же выходных данных будет выглядеть следующим образом.

    ![Диаграмма с областями результата интерактивного запроса Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Диаграмма с областями результата интерактивного запроса Spark")

9. Завершив работу с приложением, можно закрыть записную книжку, чтобы освободить ресурсы кластера. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить).

## <a name="next-step"></a>Дальнейшие действия

Из этой статьи вы узнали, как выполнять интерактивные запросы в Spark с помощью записной книжки Jupyter. Теперь переходите к следующей статье, в которой объясняется, как перенести зарегистрированные в Spark данные в средство бизнес-аналитики, например в Power BI или Tableau. 

> [!div class="nextstepaction"]
>[Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight](hdinsight-apache-spark-use-bi-tools.md)




