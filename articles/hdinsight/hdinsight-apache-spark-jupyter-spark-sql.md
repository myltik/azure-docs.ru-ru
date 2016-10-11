<properties
	pageTitle="Создание кластера Spark в HDInsight на платформе Linux и использование запросов Spark SQL из Jupyter для интерактивного анализа | Microsoft Azure"
	description="Пошаговые инструкции по быстрому созданию кластера Apache Spark в HDInsight и последующему использованию запросов Spark SQL из записных книжек Jupyter для выполнения интерактивных запросов."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/05/2016"
	ms.author="nitinme"/>


# Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark

Узнайте, как создать кластер Apache Spark в HDInsight и выполнять интерактивные запросы Spark SQL в кластере Spark с помощью записной книжки [Jupyter](https://jupyter.org).

   ![Приступая к работе с Apache Spark в HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Руководство по началу работы с Apache Spark в HDInsight. Описание шагов: создание учетной записи хранения; создание кластера; выполнение инструкций Spark SQL")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Предварительные требования

- **Подписка Azure.**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Клиент SSH**: в системах Linux, Unix и OS X клиент SSH предоставляется с помощью команды `ssh`. Для систем Windows рекомендуем использовать [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Ключи SSH** (необязательно): для защиты учетной записи SSH, которая используется для подключения к кластеру с использованием пароля или открытого ключа. Использование пароля позволяет быстро начать работу, и этот вариант следует использовать, если вы хотите быстро создать кластер и запустить некоторые тестовые задания. Использование ключа более безопасно, но требует дополнительной настройки. Этот вариант вы можете использовать при создании рабочего кластера. В этой статье мы используем вариант с паролем. Инструкции по созданию и использованию ключей SSH в HDInsight см. в следующих статьях:

	-  на компьютере под управлением Linux — [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md);
    
	-  на компьютере под управлением Windows — [Использование SSH с кластерами HDInsight (Hadoop) под управлением Linux в Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] В этой статье описано, как с помощью шаблона Azure Resource Manager создать кластер Spark, который использует [хранилище BLOB-объектов Azure в качестве хранилища кластера](hdinsight-hadoop-use-blob-storage.md). Кроме того, вы можете создать кластер Spark, использующий [хранилище озера данных Azure](../data-lake-store/data-lake-store-overview.md) в качестве дополнительного хранилища (в придачу к BLOB-объектам Azure, которые служат основными хранилищами). Инструкции см. в статье [Создание кластера HDInsight с хранилищем озера данных](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### Требования к контролю доступа

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## Создание кластера Spark

В этом разделе мы создадим кластер HDInsight версии 3.4 (Spark версии 1.6.1) с помощью шаблона Azure Resource Manager. Дополнительную информацию о различных версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Версии компонентов НDInsight](hdinsight-component-versioning.md). Другие способы создания кластера см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон хранится в общедоступном контейнере BLOB-объектов *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*.
   
2. В колонке "Параметры" заполните следующие поля:

    - **Имя кластера**. Введите имя создаваемого кластера Hadoop.
    - **Имя для входа и пароль кластера**. По умолчанию для входа используется имя admin.
    - **Имя пользователя SSH и пароль**.
    
    Запишите эти значения. Они потребуются позже в данном руководстве.

    > [AZURE.NOTE] SSH используется для удаленного доступа к кластеру HDInsight с помощью командной строки. Эти имя пользователя и пароль будут использоваться при подключении к кластеру через SSH. Имя пользователя SSH должно быть уникальным, так как на его основе создается учетная запись пользователя на всех узлах кластера HDInsight. Вот некоторые имена учетных записей, которые зарезервированы для служб в кластере и не должны использоваться в качестве имени пользователя SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	> Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

	> * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. Нажмите кнопку **ОК**, чтобы сохранить параметры.

4\. В колонке **Настраиваемое развертывание** щелкните раскрывающийся список **Группа ресурсов** и выберите пункт **Создать**, чтобы создать новую группу ресурсов. Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и другие связанные ресурсы.

5\. Щелкните **Условия**, а затем нажмите кнопку **Создать**.

6\. Нажмите кнопку **Создать**. Вы увидите новый элемент под названием "Идет отправка развертывания для развертывания шаблона". Процесс создания кластера и базы данных SQL занимает около 20 минут.



## Выполнение запросов Spark SQL с помощью документа Jupyter Notebook

В этом разделе мы будем выполнять запросы Spark SQL к кластеру Spark, используя документ Jupyter Notebook. Кластеры HDInsight Spark предоставляют два ядра, которые можно использовать с записными книжками Jupyter. а именно:

* **PySpark** (для приложений, написанных на языке Python).
* **Spark** (для приложений, написанных на языке Scala);

В этой статье вы будете использовать ядро PySpark. В статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Spark в HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) можно узнать подробности о преимуществах использования ядра PySpark. Однако несколько основных преимуществ использования ядра PySpark заключаются в следующем:

* Вам не нужно задавать контексты для Spark и Hive. Они устанавливаются автоматически.
* Для запуска запросов SQL или Hive напрямую без предшествующих фрагментов кода можно использовать волшебные слова ячеек, например `%%sql`.
* Выходные данные для запросов SQL или Hive визуализируются автоматически.

### Создание документа Jupyter Notebook с помощью ядра PySpark 

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.

2. В колонке кластера Spark щелкните **Панель мониторинга кластера**, а затем выберите **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

4. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. Можно начать с импорта различных типов, необходимых для этого сценария. Для этого вставьте следующий фрагмент кода в ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**.

		from pyspark.sql.types import *
		
	При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

	 ![Состояние задания записной книжки Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Состояние задания записной книжки Jupyter")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**.

	Вставьте указанный ниже пример кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот пример кода регистрирует данные во временной таблице с именем **hvac**.

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

5. Так как вы используете ядро PySpark, вы можете отправить SQL-запрос непосредственно к временной таблице **hvac**, которую вы только что создали с помощью волшебного слова `%%sql`. Дополнительные сведения о волшебном слове `%%sql`, а также других волшебных словах, доступных в ядре PySpark, см. в статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Spark в HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. После успешного выполнения задания по умолчанию будет показаны следующие табличные данные.

 	![Табличные выходные данные для результата запроса](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Табличные выходные данные для результата запроса")

	Результаты также можно просмотреть и в других визуализациях. Например, диаграмма областей для тех же выходных данных будет выглядеть следующим образом.

	![Диаграмма областей для результата запроса](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Диаграмма областей для результата запроса")


6. Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Закрыть и остановить**. Это завершит работу записной книжки и закроет ее.

##Удаление кластера

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Дополнительные материалы


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Analyze Application Insights telemetry logs with Spark on HDInsight (Анализ журналов телеметрии Application Insights с помощью Spark в HDInsight)](hdinsight-spark-analyze-application-insight-logs.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Управление ресурсами

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

<!---HONumber=AcomDC_1005_2016-->