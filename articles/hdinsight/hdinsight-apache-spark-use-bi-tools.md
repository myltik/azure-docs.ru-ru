---
title: "Использование средств визуализации данных с помощью Spark BI в Azure HDInsight | Документация Майкрософт"
description: "Используйте средства визуализации данных для аналитики с помощью Apache Spark BI в кластерах HDInsight"
keywords: "apache spark bi, spark bi, визуализация данных spark, бизнес-аналитика spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 8e550b8caaece7a27612686135752336483aa662
ms.contentlocale: ru-ru
ms.lasthandoff: 06/10/2017


---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight

Узнайте, как использовать средства визуализации данных, такие как Power BI и Tableau, для анализа набора необработанных демонстрационных данных с помощью Apache Spark BI в кластерах HDInsight.

> [!NOTE]
> Возможности подключения к инструментам бизнес-аналитики, описываемые в этой статье, не поддерживаются в Spark 2.1 в Azure HDInsight 3.6 (предварительная версия). Поддерживается только Spark версий 1.6 и 2.0 (HDInsight 3.4 и HDInsight 3.5 соответственно).
>

Это руководство также доступно в виде записной книжки Jupyter в кластере HDInsight Spark. Фрагменты кода Python можно выполнять непосредственно в записной книжке. Чтобы выполнить действия в руководстве из записной книжки, создайте кластер Spark, запустите записную книжку Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), а затем запустите записную книжку **Использование средств бизнес-аналитики с Apache Spark в HDInsight.ipynb** в папке **Python**.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Подготовка данных для визуализации данных Spark

В этом разделе мы используем записную книжку [Jupyter](https://jupyter.org) из кластера HDInsight Spark, для выполнения заданий, которые обрабатывают необработанные демонстрационные данные и сохраняют их в виде таблицы. В качестве демонстрационных данных выступает CSV-файл (hvac.csv), доступный на всех кластерах по умолчанию.

После сохранения данных в виде таблицы можно переходить к следующему разделу, в котором мы используем средства бизнес-аналитики для подключения к таблице и последующей визуализации данных.

1. На начальной панели [портала Azure](https://portal.azure.com/)щелкните плитку кластера Spark (если она закреплена на начальной панели). Кроме того, вы можете перейти к кластеру, последовательно щелкнув **Просмотреть все** > **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Панель мониторинга кластера**, а затем выберите **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

   > [!NOTE]
   > Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Создайте записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

    ![Создание записной книжки Jupyter для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Создание записной книжки Jupyter для Apache Spark BI")

4. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

    ![Указание имени записной книжки для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "Указание имени записной книжки для Apache Spark BI")

5. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. Можно начать с импорта различных типов, необходимых для этого сценария. Для этого наведите курсор на ячейку и нажмите клавиши **SHIFT+ВВОД**.

        from pyspark.sql import *

6. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот фрагмент кода регистрирует данные в таблице с именем **hvac**.

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

7. Убедитесь, что таблица была успешно создана. Можно использовать волшебное слово `%%sql` для непосредственного выполнения запросов Hive. Дополнительные сведения о магической команде `%%sql`, а также других магических командах, доступных в ядре PySpark, приведены в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Вы увидите примерно следующие выходные данные:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Таблицами Hive являются только те таблицы, в которых в столбце **isTemporary** указано значение false. Такие таблицы будут храниться в хранилище метаданных, и к ним могут получать доступ средства бизнес-аналитики. В этом руководстве мы подключимся к созданной нами таблице **hvac**.

8. Убедитесь, что таблица содержит нужные данные. Вставьте следующий фрагмент кода в пустую ячейку в записной книжке и нажмите **SHIFT + ВВОД**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Завершите работу записной книжки для освобождения ресурсов. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить).

## <a name="powerbi"></a>Использование Power BI для визуализации данных Spark

> [!NOTE]
> Этот раздел применим только к Spark 1.6 в HDInsight 3.4 и к Spark 2.0 в HDInsight 3.5.
>
>

После сохранения данных в виде таблицы можно воспользоваться Power BI для подключения к данным и визуализировать их для создания отчетов, панелей мониторинга и т. д.

1. Убедитесь, что у вас есть доступ к Power BI. Вы можете оформить подписку на бесплатную предварительную версию Power BI на сайте [http://www.powerbi.com/](http://www.powerbi.com/).

2. Войдите в [Power BI](http://www.powerbi.com/).

3. В нижней части области слева щелкните **Получить данные**.

4. На странице **Получение данных** в разделе **Импорт или подключение к данным** для **Базы данных** нажмите кнопку **Получить**.

    ![Получение данных в Power BI для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Получение данных в Power BI для Apache Spark BI")

5. На следующем экране выберите **Spark на Azure HDInsight**, а затем щелкните **Подключиться**. При появлении запроса введите URL-адрес кластера (`mysparkcluster.azurehdinsight.net`) и учетные данные для подключения к кластеру.

    ![Подключение к Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Подключение к Apache Spark BI")

    После установления соединения Power BI начнет импорт данных из кластера Spark в HDInsight.

6. Power BI импортирует данные и добавляет набор данных **Spark** в раздел **Наборы данных**. Щелкните по набору данных, при этом откроется новый лист для визуализации данных. Кроме того, вы можете сохранить лист в виде отчета. Чтобы сохранить лист, выберите **Сохранить** в меню **Файл**.

    ![Плитка Apache Spark BI на информационной панели Power BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Плитка Apache Spark BI на информационной панели Power BI")
7. Обратите внимание, что в списке **Поля** справа есть таблица **hvac**, которую мы создали ранее. Разверните таблицу, чтобы просмотреть все поля в таблице, как они были определены ранее в записной книжке.

      ![Список таблиц на панели мониторинга Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Список таблиц на панели мониторинга Apache Spark BI")

8. Создайте визуализацию для отображения расхождений между целевой температурой и фактической температурой для каждого здания. Для визуализации данных выберите **Диаграмма с областями** (выделена красным). Чтобы определить оси, перетащите поле **Код здания** в раздел **Ось**, а поля **ActualTemp**(Фактическая температура)/**TargetTemp** (Целевая температура) — в раздел **Значение**.

    ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

9. По умолчанию представление показывает сумму для **фактической температуры** и **целевой температуры**. Для обоих полей из раскрывающегося списка выберите **Среднее** для получения средней фактической и целевой температуры для обоих зданий.

    ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

10. Визуализация данных должна соответствовать снимку экрана ниже. Наведите указатель мыши на визуализацию, чтобы отобразить всплывающие подсказки с соответствующими данными.

    ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

11. Щелкните элемент **Save** (Сохранить) в меню в верхней части и введите имя отчета. Можно также закрепить визуализацию. При закреплении представление данных будет храниться на панели мониторинга, благодаря чему вы можете мгновенно отслеживать последние изменения.

   Для одного и того же набора данных можно добавить любое количество представлений с визуализацией и закреплять их на панели мониторинга для получения моментального снимка данных. Кроме того, кластеры Spark в HDInsight подключены к Power BI напрямую. Это означает, что Power BI всегда имеет доступ к актуальным данным из кластера, поэтому вам не требуется планировать обновления для набора данных.

## <a name="tableau"></a>Использование Tableau Desktop для визуализации данных Spark

> [!NOTE]
> Этот раздел применим только для кластеров Spark 1.5.2, созданных в Azure HDInsight.
>
>

1. Установите [Tableau Desktop](http://www.tableau.com/products/desktop) на компьютере, на котором вы выполняете действия из этого руководства по Apache Spark BI.

2. Убедитесь, что на этом компьютере также установлен драйвер ODBC Microsoft Spark. Вы можете скачать драйвер [здесь](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Запустите Tableau Desktop. В левой области в списке сервера для подключения щелкните **Spark SQL**. Если Spark SQL по умолчанию не отображается в левой области, вы можете найти его, щелкнув **Другие серверы**.
2. В диалоговом окне подключения Spark SQL укажите значения, показанные на снимке экрана ниже, и нажмите кнопку **ОК**.

    ![Подключение к кластеру для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Подключение к кластеру для Apache Spark BI")

    Если вы установили на компьютер **драйвер Microsoft ODBC Spark** , в раскрывающемся списке проверки подлинности будет пункт [служба Microsoft Azure HDInsight](http://go.microsoft.com/fwlink/?LinkId=616229) .
3. В следующем окне в раскрывающемся списке **Схема** щелкните значок **Найти**, а затем щелкните **по умолчанию**.

    ![Поиск схемы для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Поиск схемы для Apache Spark BI")
4. Для поля **Таблица** еще раз щелкните значок **Найти**, чтобы вывести список всех таблиц Hive, доступных в кластере. Вы должны увидеть таблицу **hvac** , созданную ранее с помощью записной книжки.

    ![Поиск схемы для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Поиск схемы для Apache Spark BI")
5. Перетащите таблицу в поле в правом верхней части окна. Tableau импортирует данные и отображает схему (выделено красным прямоугольником).

    ![Добавление таблиц в Tableau для Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Добавление таблиц в Tableau для Apache Spark BI")
6. Щелкните вкладку **Лист1** слева внизу. Создайте визуализацию, на которой представлены средняя целевая температура фактическая температура для всех зданий для каждой даты. Перетащите поля **Дата** и **Код здания** в раздел **Столбцы**, а поля **Actual Temp**(Фактическая температура)/**Target Temp** (Целевая температура) — в раздел **Строки**. В разделе **Метки** выберите **Область**, которая будет использоваться для визуализации данных Spark.

     ![Добавление полей для визуализации данных Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Добавление полей для визуализации данных Spark")
7. По умолчанию значения полей температуры отображаются как статистическое выражение. Если необходимо отобразить среднюю температуру, это можно сделать из раскрывающегося списка, как показано ниже.

    ![Получение среднего значения температуры для визуализации данных Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Получение среднего значения температуры для визуализации данных Spark")

8. Можно также наложить одну карту температуры на другую, чтобы лучше понять разницу между целевой и фактической температурой. Перемещайте указатель мыши в угол нижней области карты, пока курсор не примет форму красного кружка. Перетащите карту на другую карту в верхней части и отпустите кнопку мыши, когда указатель мыши примет форму красного прямоугольника.

    ![Слияние карт для визуализации данных Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Слияние карт для визуализации данных Spark")

     Визуализация данных должна измениться, как показано на снимке экрана.

    ![Выходные данные Tableau для визуализации данных Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Выходные данные Tableau для визуализации данных Spark")
9. Щелкните **Сохранить** для сохранения рабочего листа. Можно создать панели мониторинга и добавить на них один или несколько листов.

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: storage-create-storage-account.md

