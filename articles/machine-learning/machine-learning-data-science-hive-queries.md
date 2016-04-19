<properties 
	pageTitle="Отправка запросов Hive в кластеры Hadoop в процессе аналитики Кортаны | Microsoft Azure" 
	description="Обработка данных в таблицах Hive" 
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
	ms.date="02/08/2016" 
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Отправка запросов Hive в кластеры HDInsight Hadoop в процессе аналитики Кортаны

В этом документе указаны разные способы, с помощью которых вы можете отправить запросы Hive в кластеры Hadoop, которыми управляет служба HDInsight в Azure. Инструменты для отправки запросов Hive:

* командная строка Hadoop на головном узле кластера;
* IPython Notebook; 
* редактор Hive;
* сценарии Azure PowerShell. 

При этом предоставляются общие запросы Hive, которые можно использовать для поиска данных или создания признаков, использующих встроенные определяемые пользователем функции Hive.

Примеры запросов для сценариев [Данные о поездках в такси по Нью-Йорку](http://chriswhong.com/open-data/foil_nyc_taxi/) также представлены в [репозитории Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Для этих запросов уже задана схема данных, и они готовы к отправке и запуску по данному сценарию.

В последнем разделе рассматриваются параметры, настроив которые, можно повысить производительность запросов Hive.

## Предварительные требования
В этой статье предполагается, что вы:
 
* Создали учетную запись хранения Azure. Инструкции по выполнению этой задачи см. в статье [Создание учетной записи хранения в Azure](../hdinsight-get-started.md#storage). 
* Подготовили кластер Hadoop в службе HDInsight. Инструкции можно найти в разделе [Подготовка кластера HDInsight](../hdinsight-get-started.md#provision).
* Отправили данные в таблицы Hive, которые находятся в кластерах Azure HDInsight Hadoop. Если данные не загружены, необходимо предварительно загрузить их в таблицы Hive, воспользовавшись инструкциями из статьи [Создание данных и их загрузка в таблицы Hive](machine-learning-data-science-move-hive-tables.md).
* Включили удаленный доступ к кластеру. Инструкции можно найти в разделе [Доступ к головному узлу в кластере Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="submit"></a>Отправка запросов Hive

1. [Отправка запросов Hive через командную строку Hadoop в головной узел кластера Hadoop](#headnode)
2. [Отправка запросов Hive с помощью редактора Hive](#hive-editor)
3. [Отправка запросов Hive с помощью команд PowerShell Azure](#ps)
 
###<a name="headnode"></a> 1. Отправка запросов Hive через командную строку Hadoop в головной узел кластера Hadoop

Отправка сложных запросов Hive непосредственно в головном узле кластера Hadoop обычно позволяет получить ответ быстрее, чем при использовании редактора Hive или сценариев Azure PowerShell.

Войдите в головной узел кластера Hadoop, откройте командную строку Hadoop на рабочем столе головного узла и введите команду `cd %hive_home%\bin`.

Отправить запрос Hive из командной строки Hadoop можно тремя способами:

* напрямую;
* с помощью HQL-файлов;
* из командной консоли Hive.

#### Отправка запросов Hive непосредственно из командной строки Hadoop 

Отправить простой запрос Hive непосредственно из командной строки Hadoop можно с помощью такой команды, как `hive -e "<your hive query>;`. Ниже приведен пример, в котором красной рамкой обведена команда, которая отправляет запрос Hive, а зеленой обведены выходные данные обработки этого запроса.

![Создание рабочей области][10]

#### Отправка запросов Hive в HQL-файлах

Если запрос Hive сложный и содержит несколько строк, редактировать запросы в командной строке или командной консоли Hive нецелесообразно. Вместо этого можно использовать текстовый редактор в головном узле кластера Hadoop для сохранения запросов Hive в HQL-файле в локальном каталоге головного узла. Затем можно отправить запрос Hive в HQL-файле, воспользовавшись аргументом `-f`:
	
	`hive -f "<path to the .hql file>"`

![Создание рабочей области][15]


**Отменить вывод состояния хода выполнения запросов Hive на экран**

По умолчанию после отправки запроса Hive в командную строку в Hadoop на экран выводится состояние хода выполнения задания Map/Reduce. Чтобы ход выполнения задания Map/Reduce не выводился на экран, можно указать в командной строке аргумент `-S` (S в верхнем регистре) как показано ниже:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Отправка запросов Hive в командной консоли Hive

Также можно сначала войти в консоль команд Hive, выполнив команду `hive` в командной строке Hadoop, и затем отправить запросы Hive в командную консоль Hive. Ниже приведен пример. В этом примере красными рамками обведены команды, с помощью которых вы вошли в командную консоль Hive, и запрос Hive, отправленный в командную консоль Hive. Зеленой рамкой выделены выходные данные запроса Hive.

![Создание рабочей области][11]

В предыдущих примерах данные обработки запроса Hive выведены прямо на экране. Выходные данные можно также записать в локальный файл на головном узле или в большой двоичный объект Azure. Затем можно использовать другие инструменты для дальнейшего анализа выходных данных запросов Hive.

**Вывод результатов запроса Hive в локальный файл**

Чтобы вывести результаты запроса Hive в локальный каталог на головном узле, следует отправить запрос Hive из командной строки Hadoop, как показано ниже:

	`hive -e "<hive query>" > <local path in the head node>`

В следующем примере выходные данные запроса Hive записываются в файл `hivequeryoutput.txt` в каталоге `C:\apps\temp`.

![Создание рабочей области][12]

**Вывод результатов запроса Hive в большой двоичный объект Azure**

Результаты запроса Hive можно также выводить в большой двоичный объект Azure в используемом по умолчанию контейнере кластера Hadoop. Запрос Hive должен выглядеть следующим образом:

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

В следующем примере выходные данные запроса Hive записываются в каталог большого двоичного объекта `queryoutputdir` в используемом по умолчанию контейнере кластера Hadoop. Здесь нужно указать только имя каталога, не указывая имя большого двоичного объекта. Если указать имя каталога и имя большого двоичного объекта, появится следующее сообщение об ошибке `wasb:///queryoutputdir/queryoutput.txt`.

![Создание рабочей области][13]

При открытии используемого по умолчанию контейнера кластера Hadoop с помощью таких инструментов, как обозреватель хранилищ Azure, выходные данные запроса Hive будут выглядеть следующим образом. Чтобы извлечь большие двоичные объекты только с указанными буквами в именах, можно применить фильтр (выделенный красной рамкой).

![Создание рабочей области][14]

###<a name="hive-editor"></a> 2. Отправка запросов Hive с помощью редактора Hive

Также можно воспользоваться консолью запросов (редактор Hive), введя в браузере указанный URL-адрес `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor` (при этом вам будет предложено ввести учетные данные для входа в кластер Hadoop),

###<a name="ps"></a> 3. Отправка запросов Hive с помощью команд PowerShell Azure

Для отправки запросов Hive можно также использовать PowerShell. Инструкции см. в статье [Отправка заданий Hive с использованием PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).

## <a name="explore"></a>Исследование данных, проектирование функций и настройка параметров Hive

В этом разделе описаны следующие задачи по структурированию данных с помощью Hive в кластерах Hadoop под управлением службы Azure HDInsight:

1. [Исследование данных](#hive-dataexploration)
2. [Создание функций](#hive-featureengineering)

> [AZURE.NOTE] В примерах запросов Hive предполагается, что данные были переданы в таблицы Hive в кластере Hadoop под управлением службы Azure HDInsight. Если данные не загружены, необходимо предварительно загрузить их в таблицы Hive, воспользовавшись инструкциями из раздела [Создание данных и их загрузка в таблицы Hive](machine-learning-data-science-move-hive-tables.md).

###<a name="hive-dataexploration"></a>Исследование данных
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

###<a name="hive-featureengineering"></a>Создание функций

В этом разделе описаны способы создания признаков с помощью запросов Hive:
  
1. [Возможность создания функций на основе частоты](#hive-frequencyfeature)
2. [Риски категориальных переменных в двоичной классификации](#hive-riskfeature)
3. [Извлечение признаков из полей даты и времени](#hive-datefeature)
4. [Извлечение признаков из текстовых полей](#hive-textfeature)
5. [Вычисления расстояния между координатами GPS](#hive-gpsdistance)

> [AZURE.NOTE] После создания дополнительных признаков их можно добавить в качестве столбцов в существующую таблицу или создать новую таблицу с дополнительными признаками и первичный ключ, который можно объединить с исходной таблицей.

####<a name="hive-frequencyfeature"></a> Возможность создания функций на основе частоты

В некоторых случаях важно вычислить частоты уровней категориальной переменной или частоты сочетаний уровней нескольких категориальных переменных. Для вычисления частот можно использовать следующий сценарий:

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

####<a name="hive-riskfeature"></a> Риски категориальных переменных в двоичной классификации

Иногда в двоичной классификации нужно преобразовать нечисловые категориальные переменные в числовые признаки, заменив нечисловые уровни числовыми рисками, так как некоторые модели могут принимать только числовые признаки. В этом разделе показаны некоторые общие запросы Hive, вычисляющие значения рисков (логарифм отношения вероятностей) для категориальной переменной.


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

В этом примере для сглаживания значений рисков, вычисленных на основе данных, задаются переменные `smooth_param1` и `smooth_param2`. Значения рисков находятся в диапазоне от минус бесконечности до плюс бесконечности. Если риск больше 0, вероятность того, что целевое значение равно 1, больше 0,5.

После вычисления таблицы рисков риски можно назначить таблице, объединив ее с таблицей рисков. Объединяющий запрос Hive указан в предыдущем разделе.

####<a name="hive-datefeature"></a> Извлечение функций из полей даты и времени

Hive поставляется с набором определяемых пользователем функций для обработки полей даты и времени. В Hive формат даты и времени по умолчанию — гггг-ММ-дд 00:00:00 (например, 1970-01-01 12:21:32). В этом разделе показаны примеры извлечения дня месяца и месяца из поля даты и времени, а также примеры преобразования строки даты и времени в формате, отличном от формата по умолчанию, в строку даты и времени в формате по умолчанию.

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

В этом запросе Hive предполагается, что элемент `<datetime field>` (поле даты и времени) приведен в формате по умолчанию.

В противном случае необходимо сначала преобразовать поле даты и времени в метку времени Unix, а затем преобразовать ее в строку даты и времени в формате по умолчанию. После преобразования даты и времени в формат по умолчанию можно применить встроенные определяемые пользователем функции даты и времени для извлечения признаков.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

Если поле `<datetime field>` в этом запросе имеет формат `03/26/2015 12:04:39`, то поле `'<pattern of the datetime field>'` должно иметь формат `'MM/dd/yyyy HH:mm:ss'`. Чтобы проверить шаблон поля даты и времени, можно выполнить такую команду:

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

В этом запросе `hivesampletable` по умолчанию используется для всех кластеров Azure HDInsight Hadoop при развертывании кластеров.


####<a name="hive-textfeature"></a> Извлечение функций из текстовых полей

Предположим, что таблица Hive содержит текстовое поле, представляющее собой строку слов, разделенных пробелами. Следующий запрос извлекает длину строки и число слов в строке.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a> Вычисления расстояния между координатами GPS

Запрос, представленный в этом разделе, можно применить непосредственно к данным о поездках в такси по Нью-Йорку. Этот запрос предназначен для демонстрации применения встроенных математических функций в Hive для создания признаков.

Поля, используемые в этом запросе - GPS-координаты мест высадки и посадки: pickup\_longitude, pickup\_latitude, dropoff\_longitude, и dropoff\_latitude. Вот как выглядят запросы для расчета прямого расстояния между координатами посадки и высадки:

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

Математические уравнения для расчета расстояния между двумя координатами GPS, созданные Питером Лапису (Peter Lapisu), можно найти в статье [Movable Type Scripts](http://www.movable-type.co.uk/scripts/latlong.html). В коде Javascript функция toRad() представляет собой просто `lat_or_lon*pi/180`, преобразующий градусы в радианы. Здесь `lat_or_lon` — это широта или долгота. Так как в Hive нет функции `atan2`, но есть функция `atan`, функция `atan2` в приведенном выше запросе реализована с помощью функции `atan` на основе ее определения в [Википедии](http://en.wikipedia.org/wiki/Atan2).

![Создание рабочей области][1]

Полный список встроенных определяемых пользователем функций Hive можно найти [в руководстве по языку UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions).

## <a name="tuning"></a>Дополнительный раздел: настройка параметров Hive для ускорения обработки запросов

Параметры по умолчанию кластера Hive могут оказаться неподходящими для запросов Hive и данных, обрабатываемых запросами. В этом разделе рассматриваются параметры, настроив которые можно повысить производительность запросов Hive. Сначала необходимо добавлять запросы настройки параметров, а затем запросы обработки данных.

1. Куча Java: при работе с запросами на объединение крупных наборов данных или обработку длинных записей обычно возникает ошибка **свободное место в куче закончилось**. Для настройки можно установить требуемые значения параметров `mapreduce.map.java.opts` и `mapreduce.task.io.sort.mb`. Пример:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	Этот параметр выделяет 4 ГБ памяти куче Java и, кроме того, выделяя на сортировку больший объем памяти, повышает эффективность сортировки. При возникновении любых ошибок заданий, связанных с нехваткой памяти в куче, рекомендуется настроить его соответствующим образом.

2. Размер блока DFS: этот параметр задает минимальную единицу данных файловой системы. Например, если размер блока DFS составляет 128 МБ, тогда любые данные размером не более 128 МБ хранятся в одном блоке, тогда как для данных размером более 128 МБ выделяется несколько блоков. Очень маленький размер блока может привести к тому, что в Hadoop будет задействовано много служебных данных, так как узлу имени, чтобы найти блок, соответствующий файлу, придется обрабатывать гораздо больше запросов. При работе с гигабайтами (или большими объемами) данных рекомендуем такую настройку:

		set dfs.block.size=128m;

3. Оптимизация операций объединения в Hive: так как операции объединения на платформе Map/Reduce обычно выполняются на этапе сокращения, в некоторых случаях эффективность этих операций можно существенно повысить, запланировав объединения на этапе сопоставления (это также называется «объединением при сопоставлении»). Чтобы объединение в Hive выполнялось всегда по мере возможности, можно задать следующую настройку:

		set hive.auto.convert.join=true;

4. Указание количества модулей сопоставления: хотя Hadoop позволяет задать число модулей сокращения, число модулей сопоставления, как правило, задать нельзя. Этим числом можно в некоторой степени управлять, задав значения переменных Hadoop mapred.min.split.size и mapred.max.split.size. Размер каждой задачи сопоставления определяется следующим образом:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Как правило, значение по умолчанию для mapred.min.split.size равно 0, mapred.max.split.size — Long.MAX и dfs.block.size — 64 МБ. Как видно из размера данных, настройка этих параметров позволяет изменять количество используемых модулей сопоставления.

5. Ниже приведены некоторые другие дополнительные возможности оптимизации производительности Hive. Они позволяют настроить память, выделенную для задач сопоставления и сокращения, и могут использоваться для оптимизации производительности. Не забывайте, что значение `mapreduce.reduce.memory.mb` не может превышать объем физической памяти любого рабочего узла в кластере Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png


 

<!---HONumber=AcomDC_0406_2016-->