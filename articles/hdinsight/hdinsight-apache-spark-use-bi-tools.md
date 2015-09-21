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
	ms.date="09/03/2015" 
	ms.author="nitinme"/>


# Использование средств бизнес-аналитики с Apache Spark в Azure HDInsight

Узнайте о том, как использовать Apache Spark в Azure HDInsight для выполнения следующих задач:

* использование необработанных демонстрационных данных и сохранение их как таблицу Hive;
* использование средств бизнес-аналитики, таких как Power BI и Tableau, для анализа и визуализации данных.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark. Инструкции см. в разделе [Подготовка кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Компьютер с установленным драйвером ODBC Microsoft Spark (требуется для работы Spark в HDInsight с Tableau). Драйвер можно скачать [здесь](http://go.microsoft.com/fwlink/?LinkId=616229).
- Средства бизнес-аналитики, такие как [Power BI](http://www.powerbi.com/) или [Tableau Desktop](http://www.tableau.com/products/desktop). Оформить подписку на бесплатную предварительную версию Power BI можно на сайте [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Сохранение необработанных данных в виде таблицы Hive

В этом разделе мы используем записную книжку [Jupyter](https://jupyter.org), связанную с кластером Apache Spark в HDInsight, для выполнения заданий, которые обрабатывают необработанные демонстрационные данных и сохраняют их как таблицу Hive. В качестве демонстрационных данных выступает CSV-файл (hvac.csv), доступный на всех кластерах по умолчанию.

После сохранения данных в виде таблицы Hive можно переходить к следующему разделу и подключится к таблице Hive с помощью средств бизнес-аналитики, таких как Power BI и Tableau.

1. На начальной панели [портала предварительной версии Azure](https://ms.portal.azure.com/) щелкните плитку кластера Spark (если она закреплена на начальной панели). Можно также перейти к кластеру в разделе **Просмотреть все** > **Кластеры HDInsight**. 
 
2. Запустите записную книжку [Jupyter](https://jupyter.org). В колонке кластера Spark щелкните **Быстрые ссылки**, а затем в колонке **Панель мониторинга кластера** выберите **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера Spark.

2. Создайте новую записную книжку. Щелкните **New** (Создать), а затем выберите пункт **Python 2**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.CreateNotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.Notebook.Name.png "Указание имени для записной книжки")

4. Импортируйте необходимые модули и создайте контексты Spark и Hive. Вставьте следующий фрагмент кода в пустую ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**.

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext

		# Create Spark and Hive contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		hiveCtx = HiveContext(sc)

	При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Busy)** (Занято), а также заголовок записной книжки. Кроме того, рядом с надписью **Python 2** в верхнем правом углу окна отобразится закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

	 ![Состояние задания записной книжки Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Job.Status.png "Состояние задания записной книжки Jupyter")

4. Загрузите демонстрационные данные во временную таблицу. При подготовке кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\\HdiSamples\\SensorSampleData\\hvac**.

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**. Этот фрагмент кода регистрирует данные в таблице Hive, которая называется **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
		hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
		
		# Infer the schema and create a table		
		hvacTable = hiveCtx.inferSchema(hvac)
		hvacTable.registerAsTable("hvactemptable")
		hvacTable.saveAsTable("hvac")

5. Убедитесь, что таблица была успешно создана. Вставьте следующий фрагмент кода в пустую ячейку в записной книжке и нажмите сочетание клавиш **SHIFT+ВВОД**.

		hiveCtx.sql("SHOW TABLES").show()

	Должны отобразиться выходные данные, аналогичные указанным ниже.

		tableName       isTemporary
		hvactemptable   true       
		hivesampletable false      
		hvac            false

	Только в таблицах Hive в столбце **isTemporary** указано значение false. Такие таблицы будут храниться в метахранилище, предоставляя возможность доступа средствам бизнес-аналитики. В этом учебнике мы подключимся к только что созданной таблице **hvac**.

6. Убедитесь, что таблица содержит нужные данные. Вставьте следующий фрагмент кода в пустую ячейку в записной книжке и нажмите сочетание клавиш **SHIFT+ВВОД**.

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
7. Теперь можно выйти из записной книжки, перезапустив ядро. В меню в верхней части страницы щелкните элемент **Kernel** (Ядро), выберите команду **Restart** (Перезапустить) и снова нажмите кнопку **Restart** (Перезапустить) в окне подтверждения действия.

	![Перезапуск ядра Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Restart.Kernel.png "Перезапуск ядра Jupyter")

##<a name="powerbi"></a>Использование Power BI для анализа данных в таблице Hive

После сохранения данных в виде таблицы Hive можно воспользоваться Power BI для подключения к данным и визуализировать их для создания отчетов, панелей мониторинга и т. д.

1. Войдите в [Power BI](http://www.powerbi.com/).

2. На экране приветствия щелкните элемент **Базы данных и другое**.

	![Получение данных в Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Get.Data.png "Получение данных в Power BI")

3. На следующем экране выберите **Spark** и нажмите кнопку **Подключить**.

4. На странице "Spark в Azure HDInsight" укажите значения для подключения к кластеру Spark и нажмите кнопку **Подключить**.

	![Подключение к кластеру Spark в HDInsight](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Connect.Spark.png "Подключение к кластеру Spark в HDInsight")

	После установления соединения Power BI начнет импорт данных из кластера Spark в HDInsight.

5. Power BI импортирует данные и отображает новую панель мониторинга. В раздел **Наборы данных** также будет добавлен новый набор данных. Щелкните плитку Spark на панели мониторинга, чтобы открыть лист для визуализации данных.

	![Плитка Spark на панели мониторинга Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Tile.png "Плитка Spark на панели мониторинга Power BI")

6. Обратите внимание, что в списке **Поля** справа находится созданная ранее таблица **hvac**. Разверните таблицу, чтобы просмотреть все поля в таблице, как они были определены ранее в записной книжке.

	  ![Список таблиц Hive](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Display.Tables.png "Список таблиц Hive")

7. Создайте визуализацию для отображения расхождений между целевой температурой и фактической температурой для каждого здания. Для этого перетащите поле **BuildingID** (Идентификатор здания) в раздел **Axis** (Оси), а значения **ActualTemp**/**TargetTemp** (Фактическая температура/целевая температура) — в раздел **Value** (Значение).

	![Создание визуализации](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.1.png "Создание визуализации")

	Кроме того, выберите элемент **Area Map** (Карта области) (выделено на снимке красным прямоугольником) для визуализации данных.

8. По умолчанию в визуализации отображается сумма значений **ActualTemp** (Фактическая температура) и **TargetTemp** (Целевая температура). Для обоих полей выберите в раскрывающемся списке пункт **Average** (Среднее), чтобы получить средние значения фактической и целевой температуры для обоих зданий.

	![Создание визуализации](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.2.png "Создание визуализации")

9. Визуализация данных должна быть аналогична указанной ниже. Наведите указатель мыши на визуализацию, чтобы отобразить всплывающие подсказки с соответствующими данными.

	![Создание визуализации](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.3.png "Создание визуализации")

10. Щелкните элемент **Save** (Сохранить) в меню в верхней части и введите имя отчета. Можно также закрепить визуализацию. При закреплении соответствующее представление будет храниться на панели мониторинга, благодаря чему вы можете мгновенно отслеживать последние изменения.

	Для одного и того же набора данных можно добавить любое количество представлений с визуализацией и закреплять их на панели мониторинга для получения моментального снимка данных. Кроме того, кластеры Spark в HDInsight подключены к Power BI напрямую. Это означает, что Power BI всегда имеет доступ к актуальным данным из кластера, поэтому вам не требуется планировать обновления для набора данных.

##<a name="tableau"></a>Использование Tableau Desktop для анализа данных в таблице Hive
	
1. Запустите Tableau Desktop. В области слева в списке серверов для подключения щелкните элемент **Spark SQL**.

2. В диалоговом окне подключения к SQL Spark укажите значения, как показано ниже, а затем нажмите кнопку **ОК**.

	![Подключение к кластеру Spark](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Connect.png "Подключение к кластеру Spark")

	Вариант **Windows** **Azure HDInsight Service** отображается в раскрывающемся списке выбора способа проверки подлинности, только если на компьютере установлен [драйвер Microsoft ODBC Spark](http://go.microsoft.com/fwlink/?LinkId=616229).

3. На следующем экране в раскрывающемся списке **Schema** (Схема) щелкните значок **Find** (Найти), а затем щелкните элемент **default** (по умолчанию).

	![Поиск схемы](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Schema.png "Поиск схемы")

4. В поле **Table** (Таблица) снова щелкните значок **Find** (Найти), чтобы вывести список всех таблиц Hive, доступных в кластере. Должна отобразиться таблица **hvac**, созданная ранее с помощью записной книжки.

	![Поиск таблиц](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Table.png "Поиск таблиц")

5. Перетащите таблицу в поле в правом верхней части окна. Tableau импортирует данные и отображает схему (выделено красным прямоугольником).

	![Добавление таблиц в Tableau](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Table.png "Добавление таблиц в Tableau")

6. Щелкните вкладку **Sheet1** (Лист1) внизу слева. Создайте визуализацию, на которой представлены средняя целевая температура фактическая температура для всех зданий для каждой даты. Перетащите элементы **Date** (Дата) и **Building ID** (Идентификатор здания) в раздел **Columns** (Столбцы), а элементы **Actual Temp**/**Target Temp** (Фактическая температура/целевая температура) в раздел **Rows** (Строки). В разделе **Marks** (Метки) выберите элемент **Area** (Область), чтобы визуализировать карты области.

	 ![Добавление полей для визуализации](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Fields.png "Добавление полей для визуализации")

7. По умолчанию значения полей температуры отображаются как статистическое выражение. Если необходимо отобразить среднюю температуру, это можно сделать из раскрывающегося списка, как показано ниже.

	![Получение средней температуры](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Temp.Avg.png "Получение средней температуры")

8. Можно также наложить одну карту температуры на другую, чтобы лучше понять разницу между целевой и фактической температурой. Наведите указатель мыши на нижнюю карту, пока курсор не примет форму маркера (обозначено красным кружком на снимке экрана ниже). Перетащите карту на другую карту в верхней части и отпустите кнопку мыши, когда указатель мыши примет форму, которая обозначена красным прямоугольником на снимке экрана ниже.

	![Слияние карт](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Merge.png "Слияние карт")

	 Визуализация данных должна измениться следующим образом:

	![Визуализация:](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Final.Visual.png "Визуализация:")
	 
9. Щелкните **Save** (Сохранить), чтобы сохранить изменения на листе. Можно создать панели мониторинга и добавить на них один или несколько листов.

##<a name="seealso"></a>См. также:

* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Быстрый запуск: подготовка Apache Spark в HDInsight и выполнение интерактивных запросов с помощью Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=Sept15_HO2-->