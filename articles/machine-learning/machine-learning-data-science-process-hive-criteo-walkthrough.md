<properties 
	pageTitle="Обработка аналитических данных и технология расширенного анализа данных на практике: использование кластеров Hadoop для обработки набора данных Criteo объемом 1 ТБ | Microsoft Azure" 
	description="Использование обработки аналитических данных и технологии расширенного анализа данных (ADAPT) для комплексного сценария, включающего использование кластера HDInsight Hadoop для создания и развертывания модели на основе общедоступного набора данных большого объема (1 ТБ)" 
	services="machine-learning,hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# Обработка аналитических данных и технология расширенного анализа данных на практике. Использование кластеров Azure HDInsight Hadoop с набором данных объемом 1 ТБ

В этом пошаговом руководстве показано комплексное использование обработки аналитических данных и технологии расширенного анализа данных (ADAPT) на примере [кластера Azure HDInsight Hadoop](http://azure.microsoft.com/services/hdinsight/) для хранения, изучения, разработки признаков и снижения частоты выборки данных в одном из общедоступных наборов данных [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Мы используем Машинное обучение Azure для создания моделей двоичной классификации и регрессии на основе этих данных. Мы также покажем, как опубликовать одну из этих моделей в качестве веб-службы.

Для выполнения заданий, представленных в этом пошаговом руководстве, можно также использовать iPython Notebook. Пользователям, которые хотят применить этот подход, следует просмотреть раздел [Пошаговое руководство Criteo по использованию подключения Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Описание набора данных Criteo

Данные Criteo представляют собой набор данных для прогнозирования переходов по рекламным объявлениям, который содержит приблизительно 370 ГБ TSV-файлов, сжатых с помощью служебной программы gzip (около 1,3 TБ без сжатия), что составляет 4,3 миллиарда записей. Эти данные получены на основе данных о переходах по ссылкам за 24 дня, предоставленных [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Для удобства специалистов по обработке и анализу данных мы разархивировали данные, доступные для экспериментирования.

Каждая запись в этом наборе данных содержит 40 столбцов:

- первый столбец — столбец с меткой, указывающий, щелкнул пользователь рекламное объявление (значение 1) или не щелкнул (значение 0); 
- следующие 13 столбцов являются числовыми; 
- последние 26 столбцов категориальные. 

Столбцы являются анонимными, и в них используется ряд перечисляемых имен — от Col1 (столбец с меткой) до Col40 (последний категориальный столбец).

Ниже приведен фрагмент первых 20 столбцов из двух наблюдений (строк) этого набора данных:

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

В этом наборе данных как в числовых столбцах, так и в категориальных столбцах есть недостающие значения. Ниже приведен простой способ обработки недостающих значений. Дополнительная информация о данных предоставляется на этапе их сохранения в таблицы Hive ниже.

**Определение.** *Коэффициент выбора рекламного объявления* — это процент выбора элементов путем щелчка в данных. В этом наборе Criteo коэффициент составляет около 3,3 % или 0,033.

## <a name="mltasks"></a>Примеры задач прогнозирования
В этом пошаговом руководстве рассмотрены два примера задач по прогнозированию:

1. **Двоичная классификация** — прогнозирует то, щелкнул ли пользователь рекламное объявление:
	- класс 0 — не щелкнул;
	- класс 1 — щелкнул.

2. **Регрессия** — прогнозирует вероятность щелчка рекламного объявления по признакам пользователя.


## <a name="setup"></a>Настройка кластера HDInsight Hadoop для обработки и анализа данных

**Примечание.** Эту задачу обычно выполняет **администратор**.

Настройте среду обработки и анализа данных Azure, чтобы создавать решения для прогнозной аналитики с помощью кластеров HDInsight, выполнив три шага:

1. [Создание учетной записи хранения](../storage-whatis-account.md). Эта учетная запись хранения используется для хранения данных в хранилище больших двоичных объектов Azure. Здесь хранятся данные, используемые в кластерах HDInsight.

2. [Настройка кластеров Azure HDInsight Hadoop для обработки и анализа данных](machine-learning-data-science-customize-hadoop-cluster.md). На этом шаге создается кластер Azure HDInsight Hadoop и на всех узлах устанавливается 64-разрядный дистрибутив Anaconda Python 2.7. При настройке кластера HDInsight следует выполнить два важных шага (описанных в этом разделе).

	* Во время создания кластера HDInsight необходимо связать учетную запись хранения, созданную на шаге 1, с этим кластером. Эта учетная запись хранения используется для доступа к данным, которые можно обработать в пределах кластера.

	* После создания кластера необходимо включить удаленный доступ к головному узлу кластера. Запомните указываемые здесь учетные данные для удаленного доступа (не те, которые были заданы при создании кластера), так как они потребуются ниже.

3. [Создание рабочей области Машинного обучения Azure](machine-learning-create-workspace.md). Эта рабочая область Машинного обучения Azure используется для создания моделей машинного обучения после просмотра исходных данных и снижения частоты выборки в кластере HDInsight.

## <a name="getdata"></a>Получение и использование данных из общедоступного источника

Доступ к набору данных [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) можно получить, щелкнув ссылку, приняв условия использования и указав имя. Снимок того, как это выглядит, показан ниже:

![Принятие условий использования Criteo](http://i.imgur.com/hLxfI2E.png)

Нажмите кнопку **Продолжить для скачивания**, чтобы ознакомиться с дополнительной информацией о наборе данных и его доступности.

Данные находятся в расположении общедоступного [хранилища больших двоичных объектов Azure](../storage-dotnet-how-to-use-blobs.md): wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. wasb относится к расположению хранилища больших двоичных объектов Azure.

1. Данные в этом общедоступном хранилище больших двоичных объектов содержатся в трех вложенных папках с разархивированными данными.
		
	1. Вложенная папка *raw/count/* содержит данные за первый 21 день — от day_00 до day_20.
	2. Вложенная папка *raw/train* содержит данные за один день — day_21.
	3. Вложенная папка *raw/test/* содержит данные за два дня — day_22 и day_23.

2. Для тех, кому требуется начать с необработанных данных gzip, эти данные также доступны в главной папке *raw/* в виде day_NN.gz, где значение NN указано в диапазоне от 00 до 23.

Альтернативный способ получения доступа к этим данным, их просмотра и моделирования, который не требует скачивания локальных файлов, описан далее в этом пошаговом руководстве на этапе создания таблиц Hive.

## <a name="login"></a>Вход в головной узел кластера

Чтобы войти в головной узел кластера, узнайте его расположение, используя портал [управления Azure](manage.windowsazure.com). Щелкните значок HDInsight в виде слона в левой части портала, а затем дважды щелкните имя кластера. Перейдите на вкладку **Конфигурация**, дважды щелкните значок «ПОДКЛЮЧЕНИЕ» в нижней части страницы и при появлении запроса введите учетные данные для удаленного доступа. Таким образом вы перейдете к головному узлу кластера.

Вот как выглядит экран при типичном первом входе в головной узел кластера:

![Вход в кластер](http://i.imgur.com/Yys9Vvm.png)

В левой части виден значок «Командная строка Hadoop». С помощью этой строки будет выполняться просмотр данных. На экране также отображаются два полезных URL-адреса — «Состояние Hadoop Yarn» и «Узел имен Hadoop». URL-адрес состояния Yarn показывает ход выполнения задания, а URL-адрес узла имен предоставляет дополнительную информацию о конфигурации кластера.

Теперь все готово для начала первой части пошагового руководства — просмотр данных с помощью Hive и подготовка данных для Машинного обучения Azure.

## <a name="hive-db-tables"></a>Создание базы данных и таблиц Hive

Чтобы создать таблицу Hive для нашего набора данных Criteo, откройте ***командную строку Hadoop*** на рабочем столе головного узла и введите имя каталога Hive с помощью следующей команды:

    cd %hive_home%\bin

**ВАЖНОЕ ПРИМЕЧАНИЕ.** **Выполняйте все команды Hive в этом пошаговом руководстве в командной строке каталога bin/ Hive выше. Таким образом вы сможете автоматически избежать любых проблем с путем. Термины «командная строка каталога Hive», «командная строка каталога bin/ Hive» и «командная строка Hadoop» будут использоваться на взаимозаменяемой основе.**

**ВАЖНОЕ ПРИМЕЧАНИЕ 2.** **Любой запрос Hive можно всегда выполнить, использовав команду Hive **cd %hive_home%\bin.

После появления командной строки Hive REPL с символом hive > просто скопируйте и вставьте запрос, чтобы выполнить его.

Код ниже создает базу данных criteo, а затем — 4 таблицы:


* *таблицу счетчиков*, созданную с дня day_00 до дня day_20; 
* *таблицу для обучения*, созданную в день day_21; 
* две *тестовые таблицы*, созданные в день day_23 и day_22 соответственно. 

Тестовый набор данных был разделен на две разные таблицы, так как один из дней — праздник, и необходимо определить, может ли модель определить разницу между праздником и другим днем по коэффициенту выбора рекламного объявления.

Ниже для удобства приведен сценарий [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql):

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Мы обратили внимание, что все эти таблицы являются внешними, так как были просто указаны расположения (wasb) хранилища больших двоичных объектов Azure.

**ЛЮБОЙ запрос Hive, указанный ниже, можно выполнить двумя способами**:

1. **Использование командной строки Hive REPL**. Первый способ — выполнить команду hive, а затем скопировать и вставить запрос выше в командную строку Hive REPL. Для этого введите следующее:

		cd %hive_home%\bin
		hive

 	Если скопировать и вставить запрос в командную строку REPL, он начнет выполняться.

2. **Сохранение запросов в файл и выполнение команды**. Второй способ — сохранить запросы в HQL-файл ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)), а затем выполнить следующую команду для отправки запроса:

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Подтверждение создания базы данных и таблицы

Далее мы подтвердим создание базы данных, выполнив команду ниже в командной строке каталога bin/ Hive:

		hive -e "show databases;"

Результат выполнения команды:

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Таким образом подтверждается создание новой базы данных criteo.

Чтобы увидеть, какие таблицы были созданы, нужно просто выполнить команду ниже в командной строке каталога bin/ Hive:

		hive -e "show tables in criteo;"

Вы увидите следующие выходные данные:

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Просмотр данных в Hive

Теперь можно приступить к базовому просмотру данных в Hive. Мы начнем с подсчета примеров в таблицах данных для обучения и тестирования.

### Количество примеров для обучения

Ниже показано содержимое сценария [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql):

		SELECT COUNT(*) FROM criteo.criteo_train;

Мы получаем такой результат:

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

Кроме того, можно выполнить команду ниже в командной строке каталога bin/ Hive:

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Количество тестовых примеров в двух тестовых наборах данных

Теперь нужно подсчитать количество примеров в двух тестовых наборах данных. Ниже показано содержимое сценария [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql):

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Мы получаем такой результат:
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Как правило, можно также вызвать сценарий в командной строке каталога bin/ Hive, используя следующую команду:

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Наконец, мы просмотрим количество тестовых примеров в тестовом наборе данных на основе показателя day_23.

Команда для выполнения этого действия похожа на команду выше (см. [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Результат выполнения команды:
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### Распределение меток в наборе данных для обучения

Необходимо определить распределение меток в наборе данных для обучения. Чтобы увидеть его, мы покажем содержимое сценария [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Мы получаем такое распределение меток:

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

Обратите внимание, что доля положительных меток составляет около 3,3 % (в соответствии с исходным набором данных).
		
### Распределения гистограммы некоторых числовых переменных в наборе данных для обучения

Можно использовать функцию Hive histogram_numeric, чтобы узнать, как выглядит распределение числовых переменных. Ниже представлено содержимое сценария [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Мы получаем следующий результат:

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

Сочетание параметров LATERAL VIEW и explode в Hive служит для вывода выходных данных, подобных SQL, вместо обычного списка. Обратите внимание, что в таблице выше первый столбец соответствует центру каталога bin, а второй — его частоте.

### Приблизительные процентили для некоторых числовых переменных в наборе данных для обучения

Кроме того, нам необходимо вычислить приблизительные процентили для числовых переменных. Функция Hive percentile_approx делает это автоматически. Ниже представлено содержимое сценария [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql):

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Мы получаем такой результат:

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

Обратите внимание, что обычно распределение процентилей тесно связано с распределением гистограммы для любой числовой переменной.

### Поиск количества уникальных значений для некоторых категориальных столбцов в наборе данных для обучения

Мы продолжаем просматривать данные, и теперь нам нужно найти количество уникальных значений, принимаемых некоторыми категориальными столбцами. Для этого следует показать содержимое сценария [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Мы получаем такой результат:

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Обратите внимание, что в столбце Col15 найдено 19 млн. уникальных значений! Невозможно кодировать такие многомерные категориальные переменные с помощью таких упрощенных методов, как «прямое кодирование». Для эффективного решения этой проблемы объясняется и демонстрируется эффективный и надежный метод, называемый [обучение с использованием счетчиков](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx).

Этот подраздел завершается просмотром количества уникальных значений для других категориальных столбцов. Ниже приведено содержимое сценария [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql):

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

Мы получаем такой результат:

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

И снова видно, что во всех столбцах, за исключением Col20, содержится много уникальных значений.

### Подсчет совместного вхождения пар категориальных переменных в наборе данных для обучения

Необходимо подсчитать совместное вхождение пар категориальных переменных. Это можно определить с помощью кода в сценарии [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

В этом случае количество приведено в обратном порядке по порядку вхождения и рассмотрено 15 первых значений. Результат выполнения команды:

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Снижение частоты выборки в наборе данных для Машинного обучения Azure

После просмотра наборов данных и демонстрации того, как можно выполнить этот тип просмотра для любых переменных (включая сочетания переменных), мы приступим к снижению частоты выборки в наборах данных для создания моделей в Машинном обучении Azure. Помните, что рассматривается следующая задача: при заданном наборе примеров атрибутов (значений признаков от Col2 до Col40) нужно спрогнозировать, какое значение примет Col1 — 0 (без щелчка) или 1 (щелчок).

Чтобы снизить частоту выборки в наших наборах данных для обучения и тестовых наборах данных до 1 % от исходного размера, будет использована функция Hive RAND(). Следующий сценарий [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) выполняет это действие для набора данных для обучения:

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Мы получаем такой результат:

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

Сценарий [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) выполняет это действие для тестовых данных за день day_22:

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Мы получаем такой результат:

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


Сценарий [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) выполняет это действие для тестовых данных за день day_23:

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Мы получаем такой результат:

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

Такой результат позволяет использовать наши наборы данных для обучения и тестовые наборы данных со сниженной частотой выборки для создания моделей в Машинном обучении Azure.

Прежде чем мы перейдем к Машинному обучению Azure, следует обсудить последний важный компонент, касающийся таблицы счетчиков. Он подробно описан в следующем подразделе.

##<a name="count"></a> Краткое описание таблицы счетчиков

Как было видно, несколько категориальных переменных обладают очень высокой размерностью. В нашем пошаговом руководстве представлен метод под названием [обучение с использованием счетчиков](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) для кодирования этих переменных эффективным и надежным способом. Дополнительную информацию об этом методе можно получить, перейдя по указанной ссылке.

**Примечание.** В этом пошаговом руководстве рассматривается использование таблиц счетчиков для создания компактных представлений многомерных категориальных признаков. Это определенно не единственный способ кодирования категориальных признаков. Дополнительную информацию о других методах заинтересованные пользователи могут найти в статьях по [прямому кодированию](http://en.wikipedia.org/wiki/One-hot) и [хэшированию признаков](http://en.wikipedia.org/wiki/Feature_hashing).

Чтобы создать таблицы счетчиков на основе счетных данных, используются данные в папке raw/count. В разделе о моделировании показано, как создавать эти таблицы счетчиков для категориальных признаков с нуля или использовать предварительно созданную таблицу счетчиков для работы с данными. В следующих разделах термин «предварительно созданные таблицы счетчиков» употребляется в значении предоставляемых таблиц счетчиков. Подробные указания о том, как получить доступ к этим таблицам, приведены ниже.

## <a name="aml"></a> Создание моделей с помощью Машинного обучения Azure

Процесс создания моделей в Машинном обучении Azure состоит из следующих шагов:

1. [Получение данных из таблиц Hive и их добавление в Машинное обучение Azure](#step1)
2. [Создание эксперимента: очистка данных, выбор ученика и создание признаков в таблицах счетчиков](#step2)
3. [Обучение модели](#step3)
4. [Оценка модели на основе тестовых данных](#step4)
5. [Анализ модели](#step5)
6. [Публикация модели в качестве используемой веб-службы](#step6)

Теперь можно приступить к созданию моделей в Студии машинного обучения Azure. Наши данные со сниженной частотой выборки сохраняются в кластере в виде таблиц Hive. Чтобы считать эти данные, мы используем модуль «Читатель» Машинного обучения Azure. Учетные данные для доступа к учетной записи хранения этого кластера указаны ниже.

### <a name="step1"></a> Шаг 1. Получение данных из таблиц Hive и их добавление в Машинное обучение Azure с помощью модуля «Читатель», а также выбор данных для эксперимента машинного обучения

Для модуля **Читатель** значения параметров, заданные на рисунке, указаны в качестве примеров. Ниже представлены общие указания по заполнению набора параметров для модуля **Читатель**.

1. Выберите пункт «Запрос Hive» в поле «Источник данных».
2. Для элемента «Запрос Hive» достаточно просто ввести SELECT * FROM <имя_вашей_базы_данных.имя_вашей_таблицы>.
3. «URI сервера Hcatalog» — если используется кластер abc, следует ввести адрес https://abc.azurehdinsight.net.
4. «Имя учетной записи пользователя Hadoop» — имя пользователя, выбранное во время введения кластера в эксплуатацию (НЕ имя пользователя для удаленного доступа).
5. «Пароль учетной записи пользователя Hadoop» — пароль для указанного имени пользователя, выбранный во время введения кластера в эксплуатацию (НЕ пароль для удаленного доступа).
6. «Расположение выходных данных» — выберите Azure.
7. «Имя учетной записи хранения Azure» — учетная запись хранения, связанная с кластером.
8. «Ключ учетной записи хранения Azure» — ключ учетной записи хранения, связанной с кластером.
9. «Имя контейнера Azure» — если используется имя кластера abc, то нужно просто ввести abc. 

Вот как выглядит модуль **Читатель** при получении данных из таблицы Hive:

![Модуль «Читатель» получает данные](http://i.imgur.com/i3zRaoj.png)

После того как модуль **Читатель** завершит получение данных (возле модуля отобразится зеленая галочка), сохраните эти данные в качестве набора данных (с соответствующим именем). Это выглядит так:

![Модуль «Читатель» сохраняет данные](http://i.imgur.com/oxM73Np.png)


Щелкните правой кнопкой мыши порт вывода модуля **Читатель**. Отобразятся параметры **Сохранение набора данных** и **Визуализировать**. Если щелкнуть параметр **Визуализировать**, отобразится 100 строк с данными, а также правая панель, использующаяся для получения сводных статистических данных. Чтобы сохранить данные, выберите **Сохранение набора данных** и следуйте указаниям.

Чтобы выбрать сохраненный набор данных, который будет использоваться в эксперименте машинного обучения, найдите его с помощью поля **Поиск** ниже. Затем просто введите часть имени набора данных, чтобы получить доступ к нему, и перетащите набор данных на главную панель. При этом набор данных автоматически выбирается для использования в моделировании машинного обучения.

![Поиск набора данных](http://i.imgur.com/rx4nnUH.png)

Выполните эту процедуру для набора данных для обучения и тестового набора данных.

### <a name="step2"></a> Шаг 2. Создание простого эксперимента в Машинном обучении Azure для прогнозирования частоты выбора рекламного объявления

Сначала мы покажем архитектуру простого эксперимента, а затем детальнее рассмотрим его особенности. Сначала мы очистим данные, затем выберем ученика и наконец покажем, как создать признаки в предварительно созданных таблицах счетчиков или таблицах счетчиков, созданных пользователем с нуля.

![Архитектура эксперимента](http://i.imgur.com/R4iTLYi.png)

Чтобы рассмотреть все подробнее, мы начнем с сохраненных наборов данных, как показано выше.

Модуль **Очистка недостающих данных** выполняет действие, заложенное в его имени, т. е. очищает недостающие данные таким способом, который может задавать пользователь. Если посмотреть на модуль, мы увидим следующее:

![Очистка недостающих данных](http://i.imgur.com/0ycXod6.png)

Здесь мы выбрали заменить все недостающие значения на 0. Есть и другие варианты, которые можно увидеть, просмотрев раскрывающиеся списки в модуле.

Затем нужно выбрать ученика. В качестве ученика будет использовано двухклассовое увеличивающееся дерево принятия решений. В частности, будет показано, как использовать признаки счетчиков для многомерных категориальных признаков, чтобы создать компактные представления нашей модели, а также выполнить эффективное обучение и тестирование.

Здесь мы плавно перейдем к обсуждению того, что представляют собой таблицы счетчиков. Это условные счетчики для класса (иногда мы также называем их условными счетчиками). По сути, они являются способом подсчета значений признака для каждого класса и использования этих подсчетов для вычисления логарифма отношения вероятностей.


#### Получение доступа к предварительно созданным таблицам счетчиков для моделирования

Чтобы получить доступ к нашим предварительно созданным таблицам счетчиков, щелкните **Коллекция**, как показано ниже:

![Галерея](http://i.imgur.com/TsWkig3.png)

Щелкнув **Коллекция**, вы перейдете на страницу, которая выглядит следующим образом:

![Главная страница коллекции](http://i.imgur.com/dmXo0KR.png)

Здесь введите в поле поиска фразу «счетчики criteo» и прокрутите список результатов. Вы должны увидеть следующее:

![Счетчики Criteo](http://i.imgur.com/JZ119Jf.png)

Щелкнув этот эксперимент, вы перейдете к странице, которая выглядит следующим образом:

![Счетчики](http://i.imgur.com/dxdjMjh.png)

На этой странице нажмите кнопку **Открыть в Студии**, чтобы скопировать эксперимент в рабочую область. При этом также автоматически копируются наборы данных. В этом случае два главных набора данных — это таблица счетчиков и метаданные счетчиков, которые описаны более подробно далее.

#### Признаки счетчиков в наборе данных

В следующих модулях в нашем эксперименте потребуется использовать предварительно созданные таблицы счетчиков. Чтобы использовать эти предварительно созданные таблицы счетчиков, введите cr_count_ на вкладке «Поиск» нового эксперимента, и вы увидите два набора данных — cr_count_cleanednulls_metadata и cr_count_table_cleanednulls. Перетащите их на панель эксперимента справа. Если щелкнуть правой кнопкой мыши порты вывода, можно визуализировать эти наборы данных.

Визуализация метаданных таблицы счетчиков выглядит так:

![Метаданные таблицы счетчиков](http://i.imgur.com/A39PIe7.png)

Обратите внимание, что метаданные содержат информацию о том, какие столбцы использовались для создания условных счетчиков, использовался ли словарь для их создания (альтернативный вариант — эскиз CM), а также данные об использованном начальном значении хэша, количестве битов хэша, использованных для хэширования признака, количестве классов и т. д.

Визуализация таблицы счетчиков выглядит так:

![Таблица счетчиков](http://i.imgur.com/NJn1EQO.png)

Мы видим, что таблица счетчиков содержит информацию об условных счетчиках для класса. Значение категориальных признаков находится в столбце «Хэш-значение», поэтому признаки хэшируются.

Как включить признаки счетчиков в наборы данных? Для этого используется модуль **Характеризатор счетчиков**, показанный ниже:

![Модуль «Характеризатор счетчиков»](http://i.imgur.com/dnMdrR1.png)

После создания таблицы счетчиков (помните, что необходимо создать условные счетчики для класса для категориальных признаков) используется модуль **Характеризатор счетчиков**, показанный выше, для включения этих признаков счетчиков в наш набор данных. Как видно, модуль **Характеризатор счетчиков** позволяет выбрать признаки для подсчета и другие дополнительные параметры, независимо от того, что нам нужно — логарифм отношения вероятностей или также счетчики.

#### Создание таблицы счетчиков с нуля

Вспомните, что в разделе «Краткое описание таблицы счетчиков» упоминалось, что кроме предварительно созданных таблиц счетчиков (которые подробно описаны в предыдущих разделах) можно также создавать собственные таблицы счетчиков с нуля.

В этом разделе будет показано, как создать таблицу счетчиков с нуля. Для этого мы используем модуль **Создание таблицы счетчиков** ниже и соответствующие параметры:

![Модуль «Создание таблицы счетчиков»](http://i.imgur.com/r7pP8Qq.png)

Ниже представлена вторая часть параметров модуля:

![Параметры модуля «Создание таблицы счетчиков»](http://i.imgur.com/PvmSh3C.png)


**Важное примечание.** Используйте СВОИ соответствующие значения для параметров кластера и учетной записи хранения.

Если щелкнуть **Выполнить**, это позволит создать таблицы счетчиков для выбранного кластера. Выходные данные, как показано выше, представляют собой таблицу счетчиков и связанные с ней метаданные. После создания этих таблиц можно создать эксперимент.


### <a name="step3"></a>Шаг 3. Обучение модели

Чтобы выбрать этот модуль, введите в поле «Поиск» текст «двухклассовое увеличивающееся» и перетащите модуль. Здесь используются значения по умолчанию ниже для ученика, который является увеличивающимся деревом принятия решений:

![Модуль «Увеличивающееся дерево принятия решений» в качестве ученика](http://i.imgur.com/dDk0Jtv.png)

Перед запуском эксперимента машинного обучения нам понадобятся три последних компонента.

Первый — это модуль «Обучение модели». Пока первый порт этого модуля принимает ученика в качестве входных данных, второй порт принимает набор данных для обучения, чтобы обучить модель. Ниже показано, как это выглядит, а также параметры, которые нужно задать в модуле.

![Соединения между модулями «Увеличивающееся дерево принятия решений» и «Обучение модели»](http://i.imgur.com/szS2xBb.png)

![Параметры модулей «Увеличивающееся дерево принятия решений» и «Обучение модели»](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> Шаг 4. Оценка модели на основе тестового набора данных

Второй компонент — способ оценки на основе тестового набора данных. Его можно получить с помощью модуля **Оценка модели**, который принимает в качестве входных данных обученную модель из данных для обучения и тестовый набор данных для создания прогнозов. Ниже показано, как это выглядит.

![Соединения между модулями «Увеличивающееся дерево принятия решений» и «Оценка модели»](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a>Шаг 5. Анализ модели

Наконец, нам требуется оценить эффективность модели. Как правило, для задач двухклассовой (двоичной) классификации хорошо подходит мера AUC. Чтобы визуализировать это, мы подключим модуль «Оценка модели» к модулю «Анализ модели». Если щелкнуть **Визуализировать** в модуле **Анализ модели**, отобразится подобная схема:

![Модуль «Анализ модели» и «Увеличивающееся дерево принятия решений»](http://i.imgur.com/0Tl0cdg.png)

В задачах двоичной (или двухклассовой) классификации хорошо подходит такая мера прогнозирования точности, как AUC. Ниже будут показаны результаты использования этой модели на основе нашего тестового набора данных. Чтобы выполнить все должным образом, щелкните правой кнопкой мыши порт вывода модуля **Анализ модели**, а затем щелкните **Визуализировать**.

![Визуализация модуля «Анализ модели»](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> Шаг 6. Публикация модели в качестве веб-службы
Большой интерес представляет возможность публиковать модели машинного обучения в качестве веб-служб. После этого можно вызывать веб-службу с использованием данных, для которых нужно создавать прогнозы, а модель в идеале должна вернуть какой-либо прогноз.

Для этого сначала нужно сохранить обученную модель как объект «Обученная модель». Это можно сделать, щелкнув правой кнопкой мыши модуль **Обучение модели** и использовав параметр «Сохранить как обученную модель».

Далее нам нужно создать порт ввода, который использует данные в том же формате, что и данные, требуемые для прогноза, и порт вывода, который возвращает расчетные метки и связанные значения вероятности.

#### Выбор нескольких строк данных для порта ввода

Теперь будет показано, как выбрать несколько строк данных для нашего порта ввода.

![Данные порта ввода](http://i.imgur.com/XqVtSxu.png)

Чтобы выбрать всего 10 строк для использования в качестве данных порта ввода, можно воспользоваться модулем «Применение преобразования SQL».

#### Веб-служба
Теперь можно приступить к выполнению небольшого эксперимента, который можно использовать для публикации веб-службы.

#### Создание входных данных для веб-службы

Так как таблица счетчиков имеет большой размер, на начальном этапе мы возьмем несколько строк тестовых данных и создадим из них выходные данные с признаками счетчиков. Это послужит в качестве формата входных данных для наших веб-служб. Такой способ показан ниже.

![Создание входных данных модуля «Увеличивающееся дерево принятия решений»](http://i.imgur.com/OEJMmst.png)

Примечание. В качестве формата входных данных теперь будут использоваться ВЫХОДНЫЕ ДАННЫЕ модуля **Характеризатор счетчиков**. После завершения выполнения этого эксперимента сохраните выходные данные модуля **Характеризатор счетчиков** в качестве набора данных. **Важное примечание.** Этот набор данных будет использоваться для входных данных в веб-службе.

#### Оценка эксперимента для публикации веб-службы

Сначала мы покажем, как это выглядит. Основная структура — модуль «Оценки модели», который принимает объект «Обученная модель» и несколько строк входных данных, созданных ранее с помощью модуля **Характеризатор счетчиков**. Мы используем модуль «Столбцы проекта», чтобы отобразить расчетные метки и значения вероятности оценки.

![Столбцы проекта](http://i.imgur.com/kRHrIbe.png)

Полезно увидеть использование модуля «Столбцы проекта» для фильтрации данных в наборе данных. Содержимое модуля показано ниже:

![Фильтрация с помощью модуля «Столбцы проекта»](http://i.imgur.com/oVUJC9K.png)

Чтобы порты ввода и вывода стали синими, просто щелкните «Подготовить веб-службу» в нижнем правом углу. Выполнение этого эксперимента также позволяет опубликовать веб-службу, щелкнув значок **ОПУБЛИКОВАТЬ ВЕБ-СЛУЖБУ** в нижнем правом углу, показанный ниже.

![Опубликовать веб-службу](http://i.imgur.com/WO0nens.png)

После публикации веб-службы вы перейдете на страницу, которая выглядит таким образом:

![](http://i.imgur.com/YKzxAA5.png)

Слева видно две ссылки для веб-служб:

* служба **ЗАПРОС-ОТВЕТ** (или RRS), предназначенная для разовых прогнозов. Именно она будет использоваться на этом семинаре; 
* служба **ПАКЕТНОГО ВЫПОЛНЕНИЯ** (BES), которая, согласно названию, используется для пакетных прогнозов и требует, чтобы данные, для которых нужно создать прогноз, находились в большом двоичном объекте Azure.

Если щелкнуть ссылку **ЗАПРОС-ОТВЕТ**, вы перейдете на страницу, которая содержит код на C#, Python и R, поставляемый изготовителем. Этот код можно использовать для вызова веб-службы. Обратите внимание, что ключ API на этой странице следует использовать для аутентификации.

Удобно скопировать этот код Рython и вставить его в новую ячейку в iPython Notebook.

Ниже показан фрагмент кода Python с правильным ключом API.

![Код Python](http://i.imgur.com/f8N4L4g.png)

Обратите внимание, что ключ API по умолчанию заменен ключом API веб-службы. Если щелкнуть «Выполнить» в этой ячейке в iPython Notebook, будет получен следующий ответ:

![Ответ iPython](http://i.imgur.com/KSxmia2.png)

Мы видим, что ответы для двух запрошенных тестовых примеров (в структуре JSON сценария Python) получены в форме «расчетные метки, расчетные вероятности». Обратите внимание, что в этом случае были выбраны значения по умолчанию кода, поставляемого изготовителем (значение 0 для всех числовых столбцов, строка «Значение» для всех категориальных столбцов).

На этом мы заканчиваем обсуждение комплексной обработки набора данных большого объема с помощью Машинного обучения Azure, которое началось с получения терабайта данных и закончилось развертыванием модели прогнозирования в облаке в качестве веб-службы.

 

<!---HONumber=July15_HO2-->