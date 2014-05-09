<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Интерактивная консоль" pageTitle="Как работать с интерактивной консолью в HDInsight | Azure" metaKeywords="" description="В этом руководстве вы узнаете, как выполнять типичные задачи, такие как передача файлов, выполнение заданий и визуализация данных, с помощью интерактивной консоли для службы HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Интерактивные консоли JavaScript и Hive в HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




# Интерактивные консоли JavaScript и Hive в HDInsight

Служба HDInsight в Microsoft Azure поставляется вместе с интерактивными консолями JavaScript и Hive. Эти консоли обеспечивают простую интерактивную среду REPL (read-evaluate-print), в которой пользователи могут вводить выражения, вычислять их, а затем запрашивать и немедленно отображать результаты задания MapReduce. Консоль JavaScript выполняет инструкции на языке Pig Latin. Консоль Hive оценивает инструкции на языке запросов Hive (Hive QL). Инструкции обоих типов компилируются в программах MapReduce. Управление заданиями Hadoop на этих консолях гораздо проще, чем удаленное подключение к головному узлу кластера Hadoop и работа напрямую с программами MapReduce.

**Консоль JavaScript**. Это командная оболочка, предоставляющая текучий интерфейс для экосистемы Hadoop. В текучем интерфейсе используется метод создания цепочки инструкций, передающий контекст одного вызова в последовательности следующему вызову в той же последовательности. Консоль JavaScript предоставляет:
		
- Доступ к кластеру Hadoop, его ресурсам и командам распределенной файловой системы Hadoop (HDFS).
- Возможность управления данными, входящими в кластер Hadoop и выходящими из него.
- Текучий интерфейс, оценивающий инструкции Pig Latin и JavaScript для определения ряда программ MapReduce по созданию рабочих процессов обработки данных.

**Консоль Hive**. Hive — это платформа хранилища данных, созданная на основе Hadoop и предоставляющая возможности управления, работы с запросами и анализа. В ней используется язык HiveQL (диалект SQL) для запроса данных, хранящихся в кластере Hadoop. Консоль Hive предоставляет:
			
- Доступ к кластеру Hadoop, его ресурсам и командам файловой системы HDFS.		
- Реализация платформы Hive, позволяющая выполнять инструкции HiveQL в кластере Hadoop.	
- Модель реляционной базы данных для HDFS, позволяющая взаимодействовать с данными, хранящимися в распределенной файловой системе, как если бы эти данные хранились в таблицах.		
На консоли JavaScript используется язык потока данных Pig Latin, а на консоли Hive — язык запросов HiveQL. 	

Pig (и консоль JavaScript) в первую очередь выберут те, кто лучше знаком со сценарным подходом, при котором для определения рабочего процесса обработки данных используется последовательность соединенных в цепочку (или текучих) преобразований. Это также хороший выбор при наличии действительно неструктурированных данных.	

Hive (и соответствующую консоль) предпочтут те, кто лучше знаком с SQL и средой реляционной базы данных. Использование в Hive схемы и табличной абстракции означает, что процесс очень близок к тому, что обычно происходит в РСУБД.

Pig и Hive представляют языки более высокого уровня, которые компилируются в программах MapReduce, написанных на языке Java, и работают в HDFS. Если вам требуется по-настоящему точный контроль или высокая производительность, необходимо создавать программы MapReduce напрямую.


##В этом учебнике рассматриваются следующие темы

* [Использование консоли JavaScript для выполнения задания MapReduce](#runjob)
* [Использование консоли JavaScript для графического отображения результатов](#displayresults)
* [Использование консоли Hive для экспорта результатов в таблицу Hive](#createhivetable)
* [Использование консоли Hive для запроса данных в таблице Hive](#queryhivetable)


##<a name="runjob"></a>Использование консоли JavaScript для выполнения задания MapReduce

В этом разделе вы с помощью консоли JavaScript выполните пример приложения WordCount, поставляемый со службой HDInsight. В выполняемом здесь запросе JavaScript используется текучий API-интерфейс на основе Pig, предоставляемый интерактивной консолью.  В качестве анализируемого текстового файла здесь используется выпуск электронной книги проекта "Гутенберг" *The Notebooks of Leonardo Da Vinci* (Записки Леонардо да Винчи). Фильтр задан таким образом, чтобы результаты задания MapReduce содержали только десять наиболее часто встречающиеся слов. 

1. Выполните вход на [портал управления](https://manage.windowsazure.com).
2. Щелкните **HDINSIGHT**. Вы должны увидеть список развернутых кластеров Hadoop.
3. Щелкните имя кластера HDInsight, к которому хотите подключиться.
4. Щелкните **Управление кластером**.
5. Введите свои учетные данные, затем щелкните **Войти**.
6. На портале HDInsight щелкните **Примеры**.

	![HDI.Tiles.Samples][hdi-tiles-samples]

7. На странице **Коллекция примеров Hadoop** щелкните плитку **WordCount**. 
8. Справа вверху щелкните **WordCount.js** и сохраните файл в локальный каталог, например в папку ../downloads.

	![HDI.JsConsole.WordCountDownloads][hdi-jsconsole-wordcount]

9. В левом верхнем углу щелкните **Azure HDInsight**, чтобы вернуться на страницу панели мониторинга кластеров.
10. Щелкните **Интерактивный кластер**, чтобы открыть консоль JavaScript. 
	
	![HDI.Tiles.InteractiveConsole][hdi-tiles-interactive-console]
11. Справа вверху щелкните **JavaScript**.
12. Выполните следующую команду:

	fs.put()

13. В окне **Загрузить файл** введите следующие параметры:  

	- **Источник:** _...\downloads\Wordcount.js
 	- **Назначение:** ./WordCount.js/ 	

	![HDI.JsConsole.UploadJs][hdi-jsconsole-upload]

	Выполните обзор файла WordCount.js. Необходимо указать полный локальный путь. Одиночная точка в начале пути назначения является частью относительного адреса в HDFS.


14. Нажмите кнопку **Отправить**.

15. Выполните следующие команды, чтобы получить файл и отобразить содержимое:

		#ls
		#cat WordCount.js

	![HDI.JsConsole.JsCode][hdi-jsconsole-jscode]

	Заметьте, что функция map в JavaScript удаляет заглавные буквы из текста с помощью метода "toLowerCase()", прежде чем функция reduce подсчитывает количество вхождений слова. 
	
16. Выполните следующую команду, чтобы получить файл данных, который будет обработан с помощью задания MapReduce WordCount: 

 		#ls /example/data/gutenberg
	
17. Выполните следующую команду, чтобы выполнить программу MapReduce: 

		pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
	
	Замените admin текущим именем пользователя.

	Заметьте, что инструкции "связываются" вместе с помощью оператора "точка", а выходной файл называется *DaVinciTop10Words*. В следующем разделе вы обратитесь к выходному файлу. 

	После завершения вы должны увидеть следующее:

		pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
		2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! (View Log)

18. Если вы хотите просмотреть ход выполнения задания, прокрутите окно вправо и щелкните **Просмотреть журнал**. В этом журнале также предоставляется диагностика в случае неудачного завершения. Если задание завершено, вы увидите следующее сообщение в конце журнала:

		[main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.
	
19. Выполните следующую команду, чтобы получить выходной файл:

		#ls

	Заметьте, что создана папка DavinciTop10Words.

##<a name="displayresults"></a>Использование консоли JavaScript для графического отображения результатов
В последнем разделе вы выполнили задание MapReduce для извлечения 10 часто встречающихся слов из текстового файла. Выходной файл: ./DaVinciTop10Words.

1. Выполните следующую команду, чтобы отобразить результаты в каталоге DaVinciTop10Words:

		file = fs.read("DaVinciTop10Words")

	Результат имеет следующих вид:

		js> file=fs.read("DaVinciTop10Words")
		the	22966
		of	11228
		and	8428
		in	5737
		to	5296
		a	4791
		is	4261
		it	3073
		that	2903
		which	2544
	
2. Выполните следующую команду, чтобы проанализировать содержимое в файле данных:

		data = parse(file.data, "word, count:long")
		
3. Выполните следующую команду, чтобы получить графическое представление данных:

		graph.bar(data)
		
	![HDI.JsConsole.BarGraphTop10Words][hdi-jsconsole-bargraph-top10words]


##<a name="createhivetable"></a>Использование консоли Hive для экспорта результатов в таблицу Hive

В этом разделе вы познакомитесь с интерактивной консолью Hive. Будет создана таблица Hive из выходных данных задания MapReduce. В следующем разделе демонстрируется, как запросить данные в этой таблице. 

**Создание таблицы Hive**
	
1. В правом верхнем углу щелкните Hive, чтобы открыть консоль Hive.
	
2. Введите следующую команду, чтобы создать таблицу с двумя столбцами и именем _DaVinciWordCountTable_ из выходных данных примера приложения WordCount, сохраненных в папке "DaVinciTop10Words":
		
		CREATE EXTERNAL TABLE DaVinciWordCountTable		
		(word STRING,
		count INT)	
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'	
		STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';	

	Замените admin именем пользователя. 

	Заметьте, что таблица создается как ВНЕШНЯЯ, чтобы сохранить целевую папку независимо от таблицы. К тому же, вам требуется только указать папку, в которой расположен выходной файл, но не само имя файла.

3. Слева внизу щелкните **ВЫЧИСЛИТЬ**. 		

4. Введите следующие команды, чтобы убедиться, что таблица с двумя столбцами создана:

		SHOW TABLES;
		DESCRIBE DaVinciWordCountTable;

5. Щелкните **ВЫЧИСЛИТЬ**.
	
	![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

##<a name="queryhivetable"></a>Использование консоли Hive для запроса данных в таблице Hive

1. Выполните следующую команду, чтобы запросить 10 слов с наибольшим количеством вхождений:

		SELECT word, count
		FROM DaVinciWordCountTable
		ORDER BY count DESC LIMIT 10

	Результаты этого запроса:			

		SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
		the 22966
		of 11228
		and 8428
		in 5737
		to 5296
		a 4791
		is 4261
		it 3073
		that 2903
		which 2544

## Дальнейшие действия

Вы узнали, как запустить задание Hadoop с интерактивной консоли JavaScript и как проверить результаты задания с помощью этой консоли. Вы также узнали, как можно использовать интерактивную консоль Hive для проверки и обработки результатов задания Hadoop, создавая и запрашивая таблицу, содержащую выходные данные из программы MapReduce. Вы ознакомились с примерами инструкций Pig Latin и Hive QL, которые используются на консолях. Наконец, вы узнали, как интерактивные возможности REPL консолей JavaScript и Hive упрощают работу с кластером Hadoop. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Использование Pig с HDInsight][hdinsight-pig] 
* [Использование Hive с HDInsight][hdinsight-hive]
* [Использование MapReduce с HDInsight][hdinsight-mapreduce]

[hdinsight-pig]: /ru-ru/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-hive]: /ru-ru/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-mapreduce]: /ru-ru/manage/services/hdinsight/using-mapreduce-with-hdinsight/

[hdi-tiles-samples]: ./media/hdinsight-interactive-console/HDI.TileSamples.PNG
[hdi-jsconsole-wordcount]: ./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG
[hdi-tiles-interactive-console]: ./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG
[hdi-jsconsole-upload]: ./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG
[hdi-jsconsole-jscode]: ./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG
[hdi-jsconsole-bargraph-top10words]: ./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG
[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Подтверждение таблицы Hive")

