---
title: "Создание кластера Spark в HDInsight Azure и использование запросов Spark SQL из Jupyter для интерактивного анализа | Документация Майкрософт"
description: "Пошаговые инструкции по быстрому созданию кластера Apache Spark в HDInsight и последующему использованию запросов Spark SQL из записных книжек Jupyter для выполнения интерактивных запросов."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 791b6a5a07bb87302cb382290a355c9a14c63ff0
ms.openlocfilehash: cc1d484d40dce0b1c64f2e8cdebb9377a38705cb


---
# <a name="get-started-create-apache-spark-cluster-in-azure-hdinsight-and-run-interactive-queries-using-spark-sql"></a>Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов с помощью SQL Spark
Узнайте, как создать кластер [Apache Spark](hdinsight-apache-spark-overview.md) в HDInsight и выполнять интерактивные запросы Spark SQL в кластере Spark с помощью записной книжки [Jupyter](https://jupyter.org).

   ![Приступая к работе с Apache Spark в HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Руководство по началу работы с Apache Spark в HDInsight. Описание шагов: создание учетной записи хранения; создание кластера; выполнение инструкций Spark SQL")

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Ознакомьтесь со страницей [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free).
* **Клиент SSH**: в системах Linux, Unix и OS X клиент SSH предоставляется с помощью команды `ssh`. Дополнительные сведения для систем Windows см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md), а для систем Linux, Unix или OS X — в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!NOTE]
> В этой статье описано, как с помощью шаблона Azure Resource Manager создать кластер Spark, который использует [BLOB-объекты службы хранилища Azure в качестве хранилища кластера](hdinsight-hadoop-use-blob-storage.md). Кроме того, вы можете создать кластер Spark, использующий [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) в качестве дополнительного хранилища (в придачу к BLOB-объектам Azure, которые служат основными хранилищами). Инструкции см. в инструкциях по [созданию кластера HDInsight с Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Создание кластера Spark
В этом разделе вы создадите в HDInsight кластер Spark, используя [шаблон Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Дополнительную информацию о версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight](hdinsight-component-versioning.md). Другие способы создания кластера см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Введите следующие значения.

    ![Создание кластера Spark в HDInsight с использованием шаблона Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Создание кластера Spark в HDInsight с использованием шаблона Azure Resource Manager")

   * **Подписка.** Выберите свою подписку Azure для этого кластера.
   * **Группа ресурсов.** Создайте группу ресурсов или выберите существующую. Она используется для управления ресурсами Azure для ваших проектов.
   * **Расположение**. Выберите расположение группы ресурсов.  Это расположение также используется для хранилища кластера по умолчанию и кластера HDInsight.
   * **Имя кластера**: введите имя создаваемого кластера Hadoop.
   * **Имя для входа в кластер и пароль**: имя для входа по умолчанию — admin.
   * **Имя пользователя SSH и пароль**.

   Запишите эти значения.  Они потребуются позже в данном руководстве.

3. Установите флажок **Я принимаю указанные выше условия** и **Закрепить на панели мониторинга**, а затем нажмите кнопку **Приобрести**. Появится новый элемент под названием "Идет отправка развертывания для развертывания шаблона". Процесс создания кластера занимает около 20 минут.

## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Выполнение запросов Spark SQL с помощью документа Jupyter Notebook
В этом разделе мы будем выполнять запросы Spark SQL к кластеру Spark, используя документ Jupyter Notebook. Кластеры HDInsight Spark предоставляют два ядра, которые можно использовать с записными книжками Jupyter. а именно:

* **PySpark** (для приложений, написанных на языке Python).
* **Spark** (для приложений, написанных на языке Scala);

В этой статье вы будете использовать ядро PySpark. В статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Spark в HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-pyspark-or-spark-kernels) можно узнать подробности о преимуществах использования ядра PySpark. Однако несколько основных преимуществ использования ядра PySpark заключаются в следующем:

* Вам не нужно задавать контексты для Spark и Hive. Они устанавливаются автоматически.
* Для запуска запросов SQL или Hive напрямую без предшествующих фрагментов кода можно использовать волшебные слова ячеек, например `%%sql`.
* Выходные данные для запросов SQL или Hive визуализируются автоматически.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Создание документа Jupyter Notebook с помощью ядра PySpark

1. Откройте [портал Azure](https://portal.azure.com/).
2. В меню слева щелкните **Resoruce groups** (Группы ресурсов).
3. Выберите группу ресурсов, созданную в предыдущем разделе. Если имеется слишком много групп ресурсов, можно использовать функцию поиска. В группе отобразится два ресурса, кластер HDInsight и учетная запись хранения по умолчанию.
4. Щелкните кластер, чтобы открыть его.
 
2. В разделе **Быстрые ссылки** щелкните **Панели мониторинга кластера**, а затем — **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

   ![Панели мониторинга кластера HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-azure-portal-cluster-dashboards.png "Панели мониторинга кластера HDInsight")

   > [!NOTE]
   > Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

   ![Создание записной книжки Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Создание записной книжки Jupyter")

   Будет создана и открыта записная книжка с именем Untitled (Untitled.pynb). 

4. Щелкните имя записной книжки вверху и по желанию введите понятное имя.

    ![Указание имени для записной книжки](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")
5. Вставьте указанный ниже код в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**, чтобы выполнить его. Код импортирует типы, необходимые для этого сценария:

        from pyspark.sql.types import *

    Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода.

    ![Состояние задания записной книжки Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Состояние задания записной книжки Jupyter")

    При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)** , а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

6. Выполните следующий код, чтобы зарегистрировать демонстрационные данные во временной таблице **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Кластеры Spark в HDInsight поставляются с файлом демонстрационных данных **hvac.csv** в разделе **\HdiSamples\HdiSamples\SensorSampleData\hvac**.
    
7. Выполните следующий код для запроса данных:

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Так как вы используете ядро PySpark, вы можете отправить SQL-запрос непосредственно к временной таблице **hvac**, которую вы только что создали с помощью магической команды `%%sql`. Дополнительные сведения о волшебном слове `%%sql` , а также других волшебных словах, доступных в ядре PySpark, приведены в разделе [Ядра, доступные в записных книжках Jupyter с кластерами Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-pyspark-or-spark-kernels).

   По умолчанию выводятся следующие табличные данные.

     ![Вывод результатов запроса в виде таблицы](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Вывод результатов запроса в виде таблицы")

    Результаты также можно просмотреть и в других визуализациях. Например, диаграмма областей для тех же выходных данных будет выглядеть следующим образом.

    ![Диаграмма с областями, показывающая результат запроса](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Диаграмма с областями, показывающая результат запроса")

9. Завершив работу с приложением, можно закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить). Это завершит работу записной книжки и закроет ее.

## <a name="delete-the-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="see-also"></a>Дополнительные материалы
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



<!--HONumber=Jan17_HO2-->


