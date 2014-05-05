<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Использование Pig вместе с HDInsight" pageTitle="Использование Pig вместе с HDInsight | Azure" metaKeywords="" description="Узнайте, как использовать Pig вместе с HDInsight. Пишите инструкции на Pig Latin для анализа файла журнала приложений и выполнения запросов данных для создания выхода в целях последующего анализа." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование Pig вместе с HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />



# Использование Pig с HDInsight #


[Apache *Pig*][apachepig-home] представляет собой язык написания сценариев для выполнения заданий *MapReduce* в качестве альтернативы для написания кода Java. В этом учебнике вы используете PowerShell для выполнения некоторых инструкций Pig Latin для анализа файла журнала Apache log4j, и выполните несколько запросов данных, чтобы создать выходные данные. В этом учебнике продемонстрированы преимущества Pig и использование этого языка для упрощения выполнения заданий MapReduce. 

Язык написания сценариев Pig называется *Pig Latin*. Инструкции Pig Latin соответствуют следующей схеме процессов:   

- **Загрузка**: чтение данных для обработки в файловой системе
- **Преобразование**: обработка данных 
- **Дамп или сохранение**: вывод данных на экран или сохранение их для дальнейшей обработки

Дополнительные сведения о Pig Latin см. в [справочном руководстве по Pig Latin 1][piglatin-manual-1] и [справочном руководстве по Pig Latin 2][piglatin-manual-2].

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

* Кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-getting-started] или [Подготовка кластеров HDInsight][hdinsight-provision].
* Установите и настройте Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

**Предполагаемое время выполнения:** 30 минут

##В этой статье

* [Пример использования Pig](#usage)
* [Передача файлов данных в хранилище BLOB-объектов Azure](#uploaddata)
* [Общие сведения о Pig Latin](#understand)
* [Выполнение Pig Latin с помощью PowerShell](#powershell)
* [Дальнейшие действия](#nextsteps)
 
##<a id="usage"></a>Пример использования Pig
Базы данных хорошо подходят для небольших наборов данных и запросов с низкой степенью задержки. Однако когда дело доходит до данных большого размера и больших наборов данных, занимающих терабайты, традиционные базы данных SQL не являются идеальным решением. По мере возрастания нагрузки на базы данных и снижения производительности администраторы баз данных должны покупать больше оборудования. 

Как правило, все приложения сохраняют ошибки, исключения и другие проблемы с кодом в файл журнала, поэтому администраторы могут оценивать проблемы или создавать определенные показатели на основе данных файла журнала. Эти файлы журнала, как правило, становятся очень большими, так как содержат огромные массивы данных, которые должны быть обработаны и исследованы. 

Файлы журналов являются хорошим примером данных большого размера. Работа с данными большого размера становится очень сложной при использовании реляционных баз данных и пакетов для статистики и визуализации. Вследствие больших объемов данных и вычислений, связанных с этими данными, распараллеленное программное обеспечение, работающее на десятках, сотнях и даже тысячах серверов часто становится необходимым для расчета этих данных в приемлемые сроки. Hadoop предоставляет инфраструктуру MapReduce для создания приложений, которые обрабатывают большие объемы структурированных и неструктурированных данных посредством параллельных вычислений в больших компьютерных кластерах, отличаясь при этом большой надежностью и отказоустойчивостью.

Использование Pig снижает время, необходимое для написания программ сопоставлений и сведения. Это означает, что использование языка Java не требуется и отсутствует потребность в шаблонном коде. Вы также получаете гибкость для объединения кода Java с языком Pig. Многие сложные алгоритмы могут быть написаны с использованием менее чем пяти строчек кода Pig. доступного для понимания человеком.

Визуальное представление задач, которые будут решены по ознакомлению с этой статьей, приведены на следующих двух рисунках. На этих рисунках представлен репрезентативный пример набора данных, что демонстрирует рабочий процесс и преобразования данных по строкам кода сценария Pig. На первом рисунке приведен пример файла log4j:

![Пример всего файла][image-hdi-log4j-sample]

На втором рисунке показано преобразование данных:

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]




























##<a id="uploaddata"></a>Передача файла данных в хранилище BLOB-объектов

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию.  Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. В этой статье будет использован образец файла log4j, который входит в состав кластера HDInsight и хранится по пути *\example\data\sample.log* Сведения о передаче данных см. в разделе [Передача данных в HDInsight][hdinsight-upload-data].

Чтобы получить доступ к файлам, используйте следующий синтаксис: 

		wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

Например:

		wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

замените *mycontainer* на имя контейнера, а *mystorage* на имя учетной записи хранилища BLOB-объектов. 

Так как этот файл хранится в файловой системе по умолчанию, можно также получить доступ к файлу, используя следующее:

		wasb:///example/data/sample.log
		/example/data/sample.log



	
##<a id="understand"></a> Общие сведения о Pig Latin

На этом занятии будут рассмотрены некоторые инструкции Pig Latin, а также результаты выполнения этих инструкций. На следующем занятии вы запустите PowerShell для выполнения инструкций Pig.

1. Загрузка данных из файловой системы и отображение результатов 

		LOGS = LOAD 'wasb:///example/data/sample.log';
		DUMP LOGS;
	
	Выход аналогичен приведенному ниже:
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

2. Просмотрите каждую строку в файле данных для поиска соответствия 6 уровням ведения журналов:

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
3. Отфильтруйте строки, которые не имеют соответствий. Например, пустые строки.

		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		DUMP FILTEREDLEVELS;

	Выход аналогичен приведенному ниже:

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

4. Сгруппируйте все уровни ведения журналов по отдельным строкам:

		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		DUMP GROUPEDLEVELS;

	Выход аналогичен приведенному ниже:

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


5. Для каждой группы вычислите число вхождений каждого уровня ведения журнала. Это частоты возникновения каждого уровня ведения журнала.

		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		DUMP FREQUENCIES;
 
	Выход аналогичен приведенному ниже:

		(INFO,3355)
		(WARN,361)
		(DEBUG,15608)
		(ERROR,181)
		(FATAL,37)
		(TRACE,29950)

6. Отсортируйте частоты возникновения в порядке убывания:

		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;   

	Выход аналогичен приведенному ниже: 

		(TRACE,29950)
		(DEBUG,15608)
		(INFO,3355)
		(WARN,361)
	 	(ERROR,181)
		(FATAL,37)


##<a id="powershell"></a>Выполнение Pig Latin с помощью PowerShell

В этом разделе приведены инструкции по использованию командлетов PowerShell. Прежде чем приступить к работе с этим разделом, необходимо сначала настроить локальную среду, а затем настроить подключение к Azure. Подробные сведения см. в разделах [Приступая к работе с Azure HDInsight][hdinsight-getting-started] и [Администрирование HDInsight с помощью PowerShell][hdinsight-admin-powershell].


**Выполнение Pig Latin с помощью PowerShell**

1. Откройте окна консоли Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.
2. Задайте переменную в следующем сценарии, затем запустите его:

		$clusterName = "<HDInsightClusterName>" 

3. Выполните следующие команды, чтобы определить строку запроса Pig Latin:

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

	Можно использовать параметр -File, чтобы указать файл сценария Pig в HDFS.

4. Выполните следующий сценарий для отправки задания Pig:
		
		# Submit the Pig job
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

5. Запустите следующий сценарий, чтобы дождаться завершения задания Pig:		

		# Wait for the Pig job to complete
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Выполните следующий сценарий, чтобы напечатать выход задания Pig:
		
		# Print the standard error and the standard output of the Pig job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutpu

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	Задание Pig вычисляет частоту возникновения различных типов ведения журнала.




















 

##<a id="nextsteps"></a>Дальнейшие действия

Pig позволяет выполнять анализ данных, однако другие языки, поддерживаемые HDInsight, могут также заинтересовать вас. Hive представляет собой язык запросов, похожий на SQL, который позволяет без труда выполнять запросы данных в HDInsight, а задания MapReduce, написанные на языке Java, позволяют выполнять анализ сложных данных. Дополнительные сведения см. в следующих разделах.


* [Приступая к работе с Azure HDInsight][hdinsight-getting-started]
* [Передача данных в HDInsight][hdinsight-upload-data]
* [Использование Hive с HDInsight][hdinsight-using-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: /ru-ru/manage/services/hdinsight/howto-blob-store/
[hdinsight-upload-data]: /ru-ru/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-getting-started]: /ru-ru/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-admin-powershell]: /ru-ru/manage/services/hdinsight/administer-hdinsight-using-powershell/

[hdinsight-using-hive]: /ru-ru/manage/services/hdinsight/using-hive-with-hdinsight/

[hdinsight-provision]: /ru-ru/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-configure-powershell]: /ru-ru/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 

[Powershell-install-configure]: /ru-ru/documentation/articles/install-configure-powershell/

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png  


