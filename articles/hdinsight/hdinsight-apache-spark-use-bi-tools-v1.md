<properties 
	pageTitle="Использование средств бизнес-аналитики с Apache Spark в HDInsight | Microsoft Azure" 
	description="Пошаговые инструкции по использованию записных книжек совместно с Apache Spark для создания схем на основе необработанных данных, сохранения их в виде таблиц Hive и последующего использования средств бизнес-аналитики для анализа данных в таблице Hive" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Использование средств бизнес-аналитики с Apache Spark в HDInsight Windows (предварительная версия)

> [AZURE.NOTE] Теперь HDInsight предоставляет кластеры Spark в Linux. Сведения о том, как использовать кластеры Spark в HDInsight на платформе Linux, см. в разделе [Использование средств бизнес-аналитики с Apache Spark в Azure HDInsight (Linux)](hdinsight-apache-spark-use-bi-tools.md).

Узнайте о том, как использовать Apache Spark в Azure HDInsight для выполнения следующих задач:

* использование необработанных демонстрационных данных и сохранение их как таблицу Hive;
* использование средств бизнес-аналитики, таких как Power BI и Tableau, для анализа и визуализации данных.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Компьютер с установленным драйвером ODBC Microsoft Spark (требуется для работы Spark в HDInsight с Tableau). Вы можете скачать драйвер [здесь](http://go.microsoft.com/fwlink/?LinkId=616229).
- Средства бизнес-аналитики, такие как [Power BI](http://www.powerbi.com/) или [Tableau Desktop](http://www.tableau.com/products/desktop). Вы можете оформить подписку на бесплатную предварительную версию Power BI на сайте [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Сохранение необработанных данных в виде таблицы Hive

В этом разделе мы используем записную книжку [Jupyter](https://jupyter.org), связанную с кластером Apache Spark в HDInsight, для выполнения заданий, которые обрабатывают необработанные демонстрационные данные и сохраняют их как таблицу Hive. В качестве демонстрационных данных выступает CSV-файл (hvac.csv), доступный на всех кластерах по умолчанию.

После сохранения данных в виде таблицы Hive можно переходить к следующему разделу и подключится к таблице Hive с помощью средств бизнес-аналитики, таких как Power BI и Tableau.

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Jupyter Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **Python 2**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

4. Импортируйте необходимые модули и создайте контексты Spark и Hive. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext

		# Create Spark and Hive contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		hiveCtx = HiveContext(sc)

	При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также заголовок записной книжки. Кроме того, рядом с надписью **Python 2** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

	 ![Состояние задания записной книжки Jupyter](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.jupyter.job.status.png "Состояние задания записной книжки Jupyter")

5. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\\HdiSamples\\SensorSampleData\\hvac**.

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот фрагмент кода регистрирует данные в таблице Hive, которая называется **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
		hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
		
		# Infer the schema and create a table		
		hvacTable = hiveCtx.inferSchema(hvac)
		hvacTable.registerAsTable("hvactemptable")
		hvacTable.saveAsTable("hvac")

6. Убедитесь, что таблица была успешно создана. Вставьте следующий фрагмент кода в пустую ячейку в записной книжке и нажмите **SHIFT + ВВОД**.

		hiveCtx.sql("SHOW TABLES").show()

	Должны отобразиться выходные данные, аналогичные указанным ниже.

		tableName       isTemporary
		hvactemptable   true       
		hivesampletable false      
		hvac            false

	Таблицами Hive являются только те таблицы,в которых в столбце **isTemporary** указано значение false. Такие таблицы будут храниться в метахранилище и к ним могут получать доступ средства бизнес-аналитики. В этом учебнике мы подключимся к только что созданной таблице **hvac**.

7. Убедитесь, что таблица содержит нужные данные. Вставьте следующий фрагмент кода в пустую ячейку в записной книжке и нажмите **SHIFT + ВВОД**.

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
8. Теперь можно выйти из записной книжки, перезапустив ядро. В меню в верхней части страницы щелкните элемент **Kernel** (Ядро), выберите команду **Restart** (Перезапустить) и снова нажмите кнопку **Restart** (Перезапустить) в окне подтверждения действия.

	![Перезапуск ядра Jupyter](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.jupyter.restart.kernel.png "Перезапуск ядра Jupyter")

##<a name="powerbi"></a>Использование Power BI для анализа данных в таблице Hive

После сохранения данных в виде таблицы Hive можно воспользоваться Power BI для подключения к данным и визуализировать их для создания отчетов, панелей мониторинга и т. д.

1. Войдите в [Power BI](http://www.powerbi.com/).

2. На экране приветствия щелкните **Базы данных и другое**.

	![Получение данных в Power BI](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.get.data.png "Получение данных в Power BI")

3. На следующем экране выберите **Spark** и нажмите кнопку **Подключить**.

4. На странице Spark в Azure HDInsight укажите значения для подключения к кластеру Spark и щелкните **Подключиться**.

	![Подключение к кластеру Spark в HDInsight](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.connect.spark.png "Подключение к кластеру Spark в HDInsight")

	После установления соединения Power BI начнет импорт данных из кластера Spark в HDInsight.

5. Power BI импортирует данные и отображает новую панель мониторинга. Новый набор данных также будет добавлен в раздел **Наборы данных**. Щелкните плитку Spark на панели мониторинга, чтобы открыть лист для визуализации данных.

	![Плитка Spark на панели мониторинга Power BI](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.tile.png "Плитка Spark на панели мониторинга Power BI")

6. Обратите внимание, что в списке **Поля** справа есть таблица **hvac**, которую мы создали ранее. Разверните таблицу, чтобы просмотреть все поля в таблице, как они были определены ранее в записной книжке.

	  ![Список таблиц Hive](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.display.tables.png "Список таблиц Hive")

7. Создайте визуализацию для отображения расхождений между целевой температурой и фактической температурой для каждого здания. Чтобы сделать это, перетащите поле **Идентификатор здания** под **Оси**, а поля **Фактическая температура**/**Целевая температура** — в поля в разделе **Значение**.

	![Создание визуализации](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.visual1.png "Создание визуализации")

	Кроме того, выберите **Область карты** (показана красным) для визуализации данных.

8. По умолчанию представление показывает сумму для **Фактической температуры** и **Целевой температуры**. Для обоих полей из раскрывающегося списка выберите **Среднее** для получения средней фактической и целевой температуры для обоих зданий.

	![Создание визуализации](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.visual2.png "Создание визуализации")

9. Визуализация данных должна быть аналогична указанной ниже. Наведите указатель мыши на визуализацию, чтобы отобразить всплывающие подсказки с соответствующими данными.

	![Создание визуализации](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.powerbi.visual3.png "Создание визуализации")

10. Щелкните элемент **Save** (Сохранить) в меню в верхней части и введите имя отчета. Можно также закрепить визуализацию. При закреплении соответствующее представление будет храниться на панели мониторинга, благодаря чему вы можете мгновенно отслеживать последние изменения.

	Для одного и того же набора данных можно добавить любое количество представлений с визуализацией и закреплять их на панели мониторинга для получения моментального снимка данных. Кроме того, кластеры Spark в HDInsight подключены к Power BI напрямую. Это означает, что Power BI всегда имеет доступ к актуальным данным из кластера, поэтому вам не требуется планировать обновления для набора данных.

##<a name="tableau"></a>Использование Tableau Desktop для анализа данных в таблицах Hive
	
1. Запустите Tableau Desktop. В левой области в списке сервера для подключения щелкните **Spark SQL**.

2. В диалоговом окне подключения Spark SQL укажите значения, приведенные ниже, и нажмите кнопку **ОК**.

	![Подключение к кластеру Spark](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.connect.png "Подключение к кластеру Spark")

	Если вы установили на компьютер [драйвер Microsoft ODBC Spark](http://go.microsoft.com/fwlink/?LinkId=616229), в раскрывающемся списке проверки подлинности будет пункт **служба Azure HDInsight** **Windows**.

3. На следующем экране в раскрывающемся списке **Schema** (Схема) щелкните значок **Find** (Найти), а затем щелкните элемент **default** (по умолчанию).

	![Поиск схемы](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.find.schema.png "Поиск схемы")

4. Для поля **Таблица** еще раз щелкните значок **Найти**, чтобы вывести список всех таблиц Hive, доступных в кластере. Вы должны увидеть таблицу **hvac**, созданную ранее с помощью записной книжки.

	![Поиск таблиц](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.find.table.png "Поиск таблиц")

5. Перетащите таблицу в поле в правом верхней части окна. Tableau импортирует данные и отображает схему (выделено красным прямоугольником).

	![Добавление таблиц в Tableau](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.drag.table.png "Добавление таблиц в Tableau")

6. Щелкните вкладку **Лист1** слева внизу. Создайте визуализацию, на которой представлены средняя целевая температура фактическая температура для всех зданий для каждой даты. Перетащите поля **Дата** и **Идентификатор здания** в **Столбцы**, а поля **Фактическая температура**/**Целевая температура** — в **Строки**. В разделе **Метки** выберите **Область**, которая будет использована для визуализации области карты.

	 ![Добавление полей для визуализации](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.drag.fields.png "Добавление полей для визуализации")

7. По умолчанию значения полей температуры отображаются как статистическое выражение. Если необходимо отобразить среднюю температуру, это можно сделать из раскрывающегося списка, как показано ниже.

	![Получение средней температуры](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.temp.avg.png "Получение средней температуры")

8. Можно также наложить одну карту температуры на другую, чтобы лучше понять разницу между целевой и фактической температурой. Наведите указатель мыши на нижнюю карту, пока курсор не примет форму маркера (обозначено красным кружком на снимке экрана ниже). Перетащите карту на другую карту в верхней части и отпустите кнопку мыши, когда указатель мыши примет форму, которая обозначена красным прямоугольником на снимке экрана ниже.

	![Слияние карт](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.merge.png "Слияние карт")

	 Визуализация данных должна измениться следующим образом:

	![Визуализация:](./media/hdinsight-apache-spark-use-bi-tools-v1/hdispark.tableau.final.visual.png "Визуализация:")
	 
9. Щелкните **Сохранить** для сохранения рабочего листа. Можно создать панели мониторинга и добавить на них один или несколько листов.

##<a name="seealso"></a>См. также:

* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Быстрый запуск. Подготовка Apache Spark в HDInsight и выполнение интерактивных запросов с помощью Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0420_2016-->