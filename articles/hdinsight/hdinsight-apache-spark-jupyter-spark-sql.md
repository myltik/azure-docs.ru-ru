---
title: "Начало работы. Apache Spark и интерактивные запросы Spark SQL — Azure HDInsight | Документация Майкрософт"
description: "Краткое руководство HDInsight Spark по созданию кластера Apache Spark в HDInsight и выполнению интерактивных запросов с помощью записных книжек Jupyter."
keywords: "краткое руководство Spark,интерактивный запрос Spark,интерактивный запрос,Hdinsight Spark,Azure Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 0c59792423bfe2848ab5773746db466890228ddc
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017


---
# <a name="get-started-create-an-apache-spark-cluster-in-hdinsight-and-run-interactive-spark-sql-queries"></a>Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов Spark SQL

Узнайте, как создать кластер [Apache Spark](hdinsight-apache-spark-overview.md) в HDInsight и выполнять интерактивные запросы Spark SQL с помощью записной книжки [Jupyter](https://jupyter.org).

   ![Схема быстрого руководства, описывающая шаги по созданию кластера Apache Spark в Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Краткое руководство по Spark с использованием Apache Spark в HDInsight. Описанные действия: создание кластера; выполнение интерактивного запроса Spark")

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Ознакомьтесь со страницей [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Создание кластера HDInsight Spark

Следуя инструкциям из этого раздела, вы создадите кластер HDInsight Spark, используя [шаблон Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Другие способы создания кластера см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Введите следующие значения.

    ![Создание кластера HDInsight Spark с использованием шаблона Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Создание кластера Spark в HDInsight с использованием шаблона Azure Resource Manager")

    * **Подписка.** Выберите свою подписку Azure для этого кластера.
    * **Группа ресурсов.** Создайте группу ресурсов или выберите существующую. Она используется для управления ресурсами Azure для ваших проектов.
    * **Расположение**. Выберите расположение группы ресурсов.  Это расположение также используется для хранилища кластера по умолчанию и кластера HDInsight.
    * **Имя кластера.** Введите имя создаваемого кластера Hadoop.
    * **Версия Spark.** Выберите версию Spark, которую необходимо установить в кластере.
    * **Имя для входа в кластер и пароль**: имя для входа по умолчанию — admin.
    * **Имя пользователя SSH и пароль**.

   Запишите эти значения.  Они потребуются позже в данном руководстве.

3. Установите флажок **Я принимаю указанные выше условия** и **Закрепить на панели мониторинга**, а затем нажмите кнопку **Приобрести**. Появится новый элемент под названием "Идет отправка развертывания для развертывания шаблона". Процесс создания кластеров занимает около 20 минут.

> [!NOTE]
> В этой статье описано, как создать кластер Spark, использующий [большие двоичные объекты службы хранилища Azure в качестве хранилища кластера](hdinsight-hadoop-use-blob-storage.md). Кроме того, вы можете создать кластер Spark, использующий [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) в качестве дополнительного хранилища (в придачу к большим двоичным объектам службы хранилища Azure, которые служат основными хранилищами). Инструкции см. в инструкциях по [созданию кластера HDInsight с Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-an-interactive-spark-sql-query"></a>Выполнение интерактивного запроса Spark SQL

В этом разделе мы будем выполнять интерактивные запросы Spark SQL к созданному вами кластеру Spark, используя записную книжку Jupyter. Кластеры HDInsight Spark предоставляют три ядра, которые можно использовать с записными книжками Jupyter. а именно:

* **PySpark** (для приложений, написанных на языке Python).
* **PySpark3** (для приложений, написанных на языке Python3).
* **Spark** (для приложений, написанных на языке Scala);

В рамках этого руководства вы будете использовать ядро **PySpark** в записной книжке, из которой выполняется интерактивный запрос Spark SQL. Дополнительные сведения о ядрах см. в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md). Основные преимущества использования ядра PySpark:

* контексты Spark и Hive устанавливаются автоматически;
* для запуска интерактивных запросов SQL или Hive напрямую без предшествующих фрагментов кода можно использовать волшебные команды, например `%%sql`.
* Результаты интерактивных запросов визуализируются автоматически.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Создание документа Jupyter Notebook с помощью ядра PySpark

1. Откройте [портал Azure](https://portal.azure.com/).

2. Если закрепили кластер на панели мониторинга, щелкните элемент кластера на панели мониторинга, чтобы открыть колонку кластера.

    Если вы не закрепили кластер на панели мониторинга, в области слева щелкните **Кластеры HDInsight**, а затем выберите созданный кластер.

3. В разделе **Быстрые ссылки** щелкните **Панели мониторинга кластера**, а затем — **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

   ![Открытие записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Открытие записной книжки Jupyter для выполнения интерактивного запроса Spark SQL")

   > [!NOTE]
   > Также можно открыть записную книжку Jupyter для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Создайте записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

   ![Создание записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Создание записной книжки Jupyter для выполнения интерактивного запроса Spark SQL")

   Будет создана и открыта записная книжка с именем Untitled (Untitled.pynb).

4. Щелкните имя записной книжки вверху и по желанию введите понятное имя.

    ![Указание имени записной книжки Jupter, из которой будет выполняться интерактивный запрос Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Указание имени записной книжки Jupter, из которой будет выполняться интерактивный запрос Spark")

5. Вставьте указанный ниже код в пустую ячейку и нажмите сочетание клавиш **SHIFT + ВВОД**, чтобы выполнить код. Код импортирует типы, необходимые для этого сценария:

        from pyspark.sql.types import *

    Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода.

    ![Состояние интерактивного запроса Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-interactive-spark-query-status.png "Состояние интерактивного запроса Spark SQL")

    При каждом запуске интерактивного запроса в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания он изменится на кружок без заливки.

6. Зарегистрируйте пример набора данных в виде временной таблицы (**hvac**), выполнив следующий код:

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data frame as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Кластеры Spark в HDInsight поставляются с файлом демонстрационных данных **hvac.csv** в разделе **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Чтобы выполнить интерактивный запрос к данным, используйте следующий код.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Так как вы используете ядро PySpark, вы можете отправить интерактивный SQL-запрос непосредственно к временной таблице **hvac**, которую вы создали с помощью волшебной команды `%%sql`. Дополнительные сведения о магической команде `%%sql`, а также других магических командах, доступных в ядре PySpark, приведены в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   По умолчанию выводятся следующие табличные данные.

     ![Таблица результатов интерактивного запроса Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result.png "Таблица результатов интерактивного запроса Spark")

    Результаты также можно просмотреть и в других визуализациях. Например, диаграмма областей для тех же выходных данных будет выглядеть следующим образом.

    ![Диаграмма с областями результата интерактивного запроса Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result-area-chart.png "Диаграмма с областями результата интерактивного запроса Spark")

9. Завершив работу с приложением, можно закрыть записную книжку, чтобы освободить ресурсы кластера. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить).

## <a name="delete-the-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot-access-control"></a>Устранение неполадок управления доступом

Если при создании кластеров HDInsight возникли проблемы, ознакомьтесь с разделом [Access control requirements](hdinsight-administer-use-portal-linux.md#create-clusters) (Требования к контролю доступа).

## <a name="see-also"></a>См. также
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Analyze Application Insights telemetry logs with Spark on HDInsight (Анализ журналов телеметрии Application Insights с помощью Spark в HDInsight)](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
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
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

