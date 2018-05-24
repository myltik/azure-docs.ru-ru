---
title: Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight | Документация Майкрософт
description: Узнайте, как загружать данные и выполнять интерактивные запросы в кластерах Spark в Azure HDInsight.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/17/2018
ms.openlocfilehash: eeb0f8134d21d42c8401f58828160d613e8ef92b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302055"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight

В этом руководстве описывается, как создать кадр данных из CSV-файла и как выполнять интерактивные запросы Spark SQL к кластеру Apache Spark в HDInsight. В Spark кадр данных — это распределенная коллекция данных, упорядоченных в именованных столбцах. Она эквивалентна таблице в реляционной базе данных или фрейме данных в R/Python.
 
Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * Создание кадра данных из CSV-файла
> * Выполнение запросов к кадру данных

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* Изучите руководство по [созданию кластера Apache Spark в Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Создание кадра данных из CSV-файла

Приложения могут создавать кадры данных из существующего набора данных RDD, а также из таблицы Hive или других источников данных, используя объект SQLContext. На снимке экрана показан моментальный снимок файла hvac.csv, используемого в этом руководстве. CSV-файл содержит все кластеры HDInsight Spark. Эти данные демонстрируют колебания температуры в некоторых зданиях.
    
![Моментальный снимок данных для интерактивных запросов Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snapshot of data for interactive Spark SQL query")


1. Откройте записную книжку Jupyter, созданную на этапе выполнения предварительных требований.
2. Вставьте следующий код в пустую ячейку приложения и нажмите **SHIFT+ВВОД** для выполнения кода. Код импортирует типы, необходимые для этого сценария:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    При запуске интерактивного запроса в Jupyter в заголовке окна веб-браузера или вкладки будет отображаться состояние **(Busy)** (Занято), а также название приложения. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания он изменится на кружок без заливки.

    ![Состояние интерактивного запроса Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Состояние интерактивного запроса Spark SQL")

3. Выполните следующий код, чтобы создать кадр данных и временную таблицу **hvac**. 

    ```PySpark
    # Create an RDD from sample data
    csvFile = spark.read.csv('wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]
    > Если записная книжка создается с использованием ядра PySpark, при выполнении первой ячейки кода контексты SQL будут созданы автоматически. Вам не нужно явно создавать контексты.


## <a name="run-queries-on-the-dataframe"></a>Выполнение запросов к кадру данных

Когда таблица будет готова, выполните интерактивный запрос к данным.

1. В пустой ячейке приложения выполните следующий код:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Так как для работы записной книжки используется ядро PySpark, вы можете отправить интерактивный SQL-запрос непосредственно к временной таблице **hvac**.

   Отобразятся следующие табличные данные.

     ![Таблица результатов интерактивного запроса Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Таблица результатов интерактивного запроса Spark")

3. Результаты также можно просмотреть и в других визуализациях. Чтобы увидеть результат в виде диаграммы с областями, выберите **Область** и укажите другие значения, как показано ниже.

    ![Диаграмма с областями результата интерактивного запроса Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Диаграмма с областями результата интерактивного запроса Spark")

10. Для этого в меню **Файл** записной книжки выберите пункт **Save and Checkpoint** (Сохранить и установить контрольную точку). 

11. Если вы собираетесь ознакомиться со [следующим руководством](apache-spark-use-bi-tools.md) сейчас, не закрывайте записную книжку. В противном случае завершите работу записной книжки, чтобы освободить ресурсы кластера: в меню **Файл** записной книжки выберите **Close and Halt** (Закрыть и остановить).

## <a name="clean-up-resources"></a>Очистка ресурсов

В случае с HDInsight ваши данные хранятся в службе хранилища Azure или Azure Data Lake Store, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. Если вы планируете сразу приступить к следующему руководству, можно оставить кластер.

Откройте кластер на портале Azure и выберите **Удалить**.

![Удаление кластера HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Удаление кластера HDInsight")

Кроме того, можно выбрать имя группы ресурсов, чтобы открыть страницу группы ресурсов, а затем нажать кнопку **Удалить группу ресурсов**. Вместе с группой ресурсов вы также удалите кластер Spark в HDInsight и учетную запись хранения по умолчанию.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

* Создание кадра данных Spark.
* Выполнение запроса Spark SQL к кадру данных.

Теперь переходите к следующей статье, в которой объясняется, как перенести зарегистрированные в Spark данные в средство бизнес-аналитики, например в Power BI. 
> [!div class="nextstepaction"]
> [Анализ данных с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)

