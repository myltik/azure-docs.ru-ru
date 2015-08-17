<properties
	pageTitle="Отправка запросов Hive в кластеры Hadoop: процесс и технология расширенного анализа | Microsoft Azure"
	description="Обработка данных, которые есть в таблицах Hive, с помощью запросов Hive."
	services="machine-learning"
	documentationCenter=""
	authors="hangzh-msft"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Отправка запросов Hive в кластеры HDInsight Hadoop: процесс и технология расширенного анализа 

В этом документе указаны разные способы, с помощью которых вы можете отправить запросы Hive в кластеры Hadoop, которыми управляет служба HDInsight в Azure. Эта задача является частью процесса обработки аналитических данных и технологии расширенного анализа (ADAPT) в рамках машинного обучения Azure. Приведены несколько задач по выпасу данных: это просмотр данных и создание признаков. Общие запросы Hive, на примере которых показано, как просматривать данные и создавать признаки с помощью Hive в кластере Azure HDInsight Hadoop. Эти запросы Hive используют внедренные признаки, определяемые пользователем (они предоставлены).

Кроме того, в <a href="https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts" target="_blank">репозитории Github</a> приведены примеры запросов, которые используются для сценариев, похожих на <a href="http://chriswhong.com/open-data/foil_nyc_taxi/" target="_blank">Данные о поездках в такси по Нью-Йорку</a>. Для этих запросов уже задана схема данных, и они готовы к отправке и запуску.

В последнем разделе рассматриваются параметры, настроив которые можно повысить производительность запросов Hive.

## Предварительные требования
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции можно найти в статье [Создание учетной записи хранения в Azure](../hdinsight-get-started.md#storage).
* Подготовили настраиваемый кластер Hadoop с помощью службы HDInsight. Инструкции см. в статье [Настройка кластеров Azure HDInsight Hadoop для расширенного процесса обработки аналитических данных](machine-learning-data-science-customize-hadoop-cluster.md).
* Отправили данные в таблицы Hive, которые находятся в кластерах Azure HDInsight Hadoop. Если данные не загружены, необходимо предварительно загрузить их в таблицы Hive, воспользовавшись инструкциями из раздела [Создание данных и их загрузка в таблицы Hive](machine-learning-data-science-move-hive-tables.md).
* Включили удаленный доступ к кластеру. Инструкции можно найти в разделе [Доступ к головному узлу в кластере Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


## <a name="submit"></a>Отправка запросов Hive
Инструменты для отправки запросов Hive:

* командная строка Hadoop на головном узле кластера;
* IPython Notebook;
* редактор Hive;
* сценарии Azure PowerShell.

Запросы Hive похожи на SQL. Пользователям, знакомым с SQL, может оказаться полезной <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Памятка по сравнению SQL и Hive</a>.

Отправляя запрос Hive, вы можете указать также место назначения, в которое будут отправлены результаты обработки запроса. Местом назначения может быть экран, локальный файл в головном узле или большой двоичный объект Azure.

### Использование консоли командной строки Hadoop в головном узле кластера Hadoop

Если запрос сложный, отправка запросов Hive прямо из головного узла кластера Hadoop обычно приводит к результатам быстрее, чем отправка с помощью редактора Hive или скриптов Azure PowerShell.

Войдите в головной узел кластера Hadoop, откройте командную строку Hadoop на рабочем столе головного узла и введите команду

    cd %hive_home%\bin

Пользователи могут отправить запросы Hive с помощью консоли командной строки Hadoop тремя способами:

* прямо из командной строки Hadoop;
* с помощью HQL-файлов;
* из командной консоли Hive.

#### Отправка запросов Hive прямо из командной строки Hadoop

Чтобы отправлять простые запросы Hive прямо из командной строки Hadoop, пользователи могут выполнять команды наподобие

	hive -e "<your hive query>;

. Ниже приведен пример, в котором красной рамкой обведена команда, которая отправляет запрос Hive, а зеленой обведены выходные данные обработки этого запроса.

![Создание рабочей области][10]

#### Отправка запросов Hive в HQL-файлах

Когда запрос Hive сложный и состоит из нескольких строк, редактировать его в командной строке Hadoop или в командной консоли Hive непрактично. Вместо этого лучше с помощью текстового редактора, находящегося в головном узле кластера Hadoop, сохранить запросы Hive в HQL-файле в локальном каталоге головного узла. Затем вы можете отправить запрос Hive в HQL-файле, добавив в команду `hive` аргумент `-f`, как показано ниже.

	`hive -f "<path to the .hql file>"`


#### Отменить вывод состояния хода выполнения запросов Hive на экран

Когда запрос Hive отправлен с помощью консоли командной строки Hadoop, по умолчанию на экране отображается ход выполнения задачи сопоставления или уменьшения. Чтобы скрыть ход выполнения задачи Map/Reduce, можно добавить в командную строку аргумент `-S` (с учетом регистра), как показано ниже.

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Отправка запросов Hive в командной консоли Hive

Вы можете также войти в командную консоль Hive, запустив команду `hive` из командной строки Hadoop, а затем отправить запросы Hive уже из консоли в командной строке **hive>**. Ниже приведен пример.

![Создание рабочей области][11]

В этом примере красными рамками обведены команды, с помощью которых вы вошли в командную консоль Hive, и запрос Hive, отправленный в командную консоль Hive. Зеленой рамкой выделены выходные данные запроса Hive.

В предыдущих примерах данные обработки запроса Hive выведены прямо на экране. Выходные данные можно также записать в локальный файл на головном узле или в большой двоичный объект Azure. После этого, чтобы глубже проанализировать результаты обработки запросов Hive, вы можете использовать другие инструменты.

#### Вывод результатов запроса Hive в локальный файл

Чтобы вывести результаты запроса Hive в локальный каталог на головном узле, следует отправить запрос Hive из командной строки Hadoop, как показано ниже:

	`hive -e "<hive query>" > <local path in the head node>`


#### Вывод результатов запроса Hive в большой двоичный объект Azure

Результаты запроса Hive можно также выводить в большой двоичный объект Azure в используемом по умолчанию контейнере кластера Hadoop. Вот как выглядит соответствующий запрос Hive:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

В следующем примере выходные данные запроса Hive записываются в каталог большого двоичного объекта `queryoutputdir` в используемом по умолчанию контейнере кластера Hadoop. Здесь нужно указать только имя каталога, а имя большого двоичного объекта указывать не нужно. Если указать и каталог, и имя большого двоичного объекта, например **wasb:///queryoutputdir/queryoutput.txt*, произойдет ошибка.

![Создание рабочей области][13]

Чтобы ознакомиться с результатами обработки запроса Hive в хранилище больших двоичных объектов, нужно с помощью Azure Storage Explorer (или идентичного инструмента) открыть контейнер по умолчанию кластера Hadoop. Если вы хотите получить большой двоичный объект с определенными буквами в именах, вы можете применить фильтр (обведен красной рамкой).

![Создание рабочей области][14]

### Использование редактора Hive или команд Azure PowerShell

Вы можете использовать также консоль запросов (редактор Hive). Для этого введите в веб-браузер URL-адрес такого формата:

*имя кластера https://&#60;Hadoop>.azurehdinsight.net/Home/HiveEditor*

. Обратите внимание, что будет предложено ввести учетные данные кластера Hadoop для входа. Также вы можете [отправить задания Hive с помощью PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


##<a name="hive-dataexploration"></a>Исследование данных
Здесь представлено несколько примеров сценариев Hive, которые можно использовать для просмотра данных в таблицах Hive.

1. Получение количества наблюдений на раздел `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Получение количества наблюдений в день `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Получение уровней в столбце категорий `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Получение числа уровней в сочетании двух столбцов категорий `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Получение распределения для числовых столбцов `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Извлечение записей из двух объединенных таблиц

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

##<a name="hive-featureengineering"></a>Создание функций

В этом раздели приведены способы, с помощью которых вы можете с помощью запросов Hive создавать функции.

> [AZURE.NOTE]Когда мы приводим образцы запросов Hive в этом разделе, мы подразумеваем, что данные уже отправлены в таблицы Hive в кластерах Azure HDInsight Hadoop. Если данные не загружены, необходимо предварительно загрузить их в таблицы Hive, воспользовавшись инструкциями из раздела [Создание данных и их загрузка в таблицы Hive](machine-learning-data-science-hive-tables.md).

После создания дополнительных признаков вы можете добавить их в виде столбцов к существующей таблице или создать новую таблицу с дополнительными возможностями и первичный ключ, который затем можно объединить с исходной таблицей.

1. [Возможность создания функций на основе частоты](#hive-frequencyfeature)
2. [Риски категориальных переменных в двоичной классификации](#hive-riskfeature)
3. [Извлечение признаков из полей даты и времени](#hive-datefeatures)
4. [Извлечение признаков из текстовых полей](#hive-textfeatures)
5. [Вычисления расстояния между координатами GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Возможность создания функций на основе частоты

Часто бывает полезным вычислить частоту уровней категориальной переменной или частоту сочетания уровней нескольких категориальных переменных. Вот какой скрипт вы можете использовать для вычисления этих типов частоты.

		select
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select
				<column_name1>,<column_name2>, count(*) as sub_count
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;


###<a name="hive-riskfeature"></a>Риски категориальных переменных в двоичной классификации

В двоичной классификации нужно преобразовать нечисловые категориальные переменные в числовые функции, тогда как используемые модели принимают только числовые функции. Чтобы это сделать, нужно заменить каждый нечисловой уровень числовым риском. В этом разделе приведены некоторые общие запросы Hive, с помощью которых вы можете вычислить значения риска категориальной переменной.


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select
	    	<column_name1>,<column_name2>,
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename>
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b

В этом примере переменные `smooth_param1` и `smooth_param2` задаются для сглаживания значений риска, вычисленных на основе данных. Риски имеют диапазон от -Inf до Inf. Риск > 0 означает, что возможность того, что цель будет равной 1, превышает 0,5.

После вычисления таблицы рисков риски можно назначить таблице, объединив ее с таблицей рисков. Запрос Hive на объединение предоставлен в предыдущем разделе.

###<a name="hive-datefeatures"></a>Извлечение функций из полей даты и времени

Язык Hive включает в себя набор функций, определяемых пользователем, для обработки полей даты и времени. По умолчанию формат даты и времени в Hive выглядит так: yyyy-MM-dd 00:00:00 (например, 1970-01-01 12:21:32). В этом разделе приведены примеры того, как из поля даты и времени извлекать день месяца и месяц, и другие примеры, в которых строка даты и времени в формате не по умолчанию преобразуется в строку в формате по умолчанию.

    	select day(<datetime field>), month(<datetime field>)
		from <databasename>.<tablename>;

В этом запросе Hive предполагается, что элемент *&#60;datetime field>* приведен в стандартном формате даты и времени.

Если поле даты и времени приведено не в формате по умолчанию, нужно преобразовать его в метку времени Unix, а затем преобразовать метку времени в строку даты и времени в формате по умолчанию. Когда дата и время указаны в формате по умолчанию, вы можете применять определяемые пользователями функции даты и времени для извлечения функций.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

В этом запросе, если *&#60;datetime field>* имеет шаблон *26/03/2015 12:04:39*, то *&#60;pattern of the datetime field>* должен выглядеть так: `'MM/dd/yyyy HH:mm:ss'`. Чтобы проверить шаблон поля даты и времени, можно выполнить такую команду:

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

Компонент *hivesampletable* этого запроса по умолчанию предустановлен во всех подготовленных кластерах Azure HDInsight Hadoop.


###<a name="hive-textfeatures"></a>Извлечение функций из текстовых полей

Если таблица Hive содержит текстовое поле, содержащее строку слов, разделенных пробелами, следующий запрос извлекает длину строки и число слов в строке.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Вычисление расстояния между координатами GPS

Запрос, приведенный в этом разделе, вы можете напрямую применить к данным о поездках в такси в Нью-Йорке. Цель этого запроса — продемонстрировать, как применить внедренные математические функции в Hive, чтобы создать признаки.

Поля в этом запросе — это координаты GPS, обозначающие точки посадки и высадки пассажиров. Они называются так: *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* и *dropoff\_latitude*. Ниже приведены запросы, которые рассчитывают расстояние между координатами посадки и высадки.

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
		from nyctaxi.trip
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10;

Математические уравнения, с помощью которых рассчитывается расстояние между двумя координатами GPS, можно найти на сайте <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Скрипты для расчета перемещений</a> (создатель веб-сайта — Петер Лапису (Peter Lapisu)). В Javascript на этом сайте функция `toRad()` имеет значение *lat\_or\_lon*pi/180*, преобразующее градусы в радианы. Здесь *lat\_or\_lon* — это широта или долгота. Так как в Hive нет функции `atan2`, но есть функция `atan`, функция `atan2` в приведенном выше запросе Hive реализована функцией `atan` с помощью определения из <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Википедии</a>.

![Создание рабочей области][1]

Полный список внедренных в Hive функций, определяемых пользователями, можно найти в разделе **Встроенные функции** на <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">вики-сайте Apache Hive</a>.

## <a name="tuning"></a>Дополнительный раздел: настройка параметров Hive для ускорения обработки запросов

Настройки по умолчанию кластера Hive могут не подойти для запросов Hive и данных, обрабатываемых этими запросами. В этом разделе речь идет о некоторых параметрах, которые вы можете настраивать, чтобы улучшить производительность запросов Hive. Сначала необходимо добавлять запросы настройки параметров, а затем запросы обработки данных.

1. **Куча Java**: при работе с запросами на объединение крупных наборов данных или обработку длинных записей часто возникает ошибка, указывающая на то, что **не хватает памяти в куче**. Чтобы ее исправить, задайте нужные значения для параметров *mapreduce.map.java.opts* и *mapreduce.task.io.sort.mb*. Пример:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;


	Этот параметр выделяет 4 ГБ памяти куче Java и, кроме того, выделяя на сортировку больший объем памяти, повышает эффективность сортировки. Мы рекомендуем заняться настройкой того, сколько памяти куда выделяется, если произошли сбои задач, связанные с размером кучи.

2. **Размер блока DFS**: этот параметр задает минимальную единицу измерения данных файловой системы. Например, если размер блока DFS составляет 128 МБ, тогда любые данные размером не более 128 МБ хранятся в одном блоке, тогда как для данных размером более 128 МБ выделяется несколько блоков. Очень маленький размер блока может привести к тому, что в Hadoop будет задействовано много служебных данных, так как узлу имени, чтобы найти блок, соответствующий файлу, придется обрабатывать гораздо больше запросов. При работе с гигабайтами (или большими объемами) данных рекомендуем такую настройку:

		set dfs.block.size=128m;

3. **Оптимизация операций объединения в Hive**: так как операции объединения на платформе Map/Reduce обычно выполняются на этапе сокращения, в некоторых случаях эффективность этих операций можно существенно повысить, запланировав объединения на этапе сопоставления (они также называются «объединениями при сопоставлении»). Чтобы объединение в Hive выполнялось всегда по мере возможности, можно задать следующую настройку:

		set hive.auto.convert.join=true;

4. **Определение количества модулей сопоставления в Hive**: хотя Hadoop позволяет задать число модулей сокращения, число модулей сопоставления, как правило, задать нельзя. Этим числом можно в некоторой степени управлять, задав значения переменных Hadoop *mapred.min.split.size* и *mapred.max.split.size*, так как размер каждой задачи сопоставления определяется следующим образом:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Как правило, значение по умолчанию *mapred.min.split.size* равно 0, *mapred.max.split.size* — **Long.MAX**, а *dfs.block.size* — 64 МБ. Как видно из размера данных, настройка этих параметров позволяет изменять количество используемых модулей сопоставления.

5. Ниже приведены некоторые другие **дополнительные возможности** оптимизации производительности Hive. Они позволяют указывать объем памяти, выделяемой на сопоставление и уменьшение задач. Кроме того, с их помощью вы можете изменять производительность. Не забывайте, что значение *mapreduce.reduce.memory.mb* не может превышать объем физической памяти каждого рабочего узла в кластере Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-process-hive-tables/atan2new.png
[10]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-3.png
 

<!---HONumber=August15_HO6-->