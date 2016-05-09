<properties
   pageTitle="Использование MapReduce и удаленного рабочего стола с Hadoop в HDInsight | Microsoft Azure"
   description="Информация об использовании удаленного рабочего стола для подключения к Hadoop в HDInsight и выполнения заданий MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/22/2016"
   ms.author="larryfr"/>

# Использование MapReduce в Hadoop в HDInsight с помощью удаленного рабочего стола

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этой статье вы узнаете, как подключиться к Hadoop в кластере HDInsight с помощью удаленного рабочего стола, а затем выполнить задания MapReduce с помощью команды Hadoop.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Кластер HDInsight на платформе Windows (Hadoop в HDInsight).

* Клиентский компьютер под управлением Windows 10, Windows 8 или Windows 7.

##<a id="connect"></a>Подключение к удаленному рабочему столу

Запустите протокол удаленного рабочего стола для кластера HDInsight, а затем выполните подключение, следуя инструкциям раздела [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Использование команды Hadoop

После подключения к рабочему столу кластера HDInsight сделайте следующее, чтобы выполнить задание MapReduce с помощью команды Hadoop:

1. На рабочем столе HDInsight запустите **командную строку Hadoop**. Откроется новое окно командной строки в каталоге **c:\\apps\\dist\\hadoop-&lt;номер\_версии>**.

	> [AZURE.NOTE] При обновлении Hadoop номер версии изменяется. Для поиска пути можно использовать переменную среды **HADOOP\_HOME**. Например, `cd %HADOOP_HOME%` изменит каталоги на каталог Hadoop без необходимости знать номер версии.

2. Чтобы выполнить пример задания MapReduce с помощью команды **Hadoop**, используйте следующую команду:

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	При этом запустится класс **wordcount**, содержащийся в текущем каталоге в файле **hadoop-mapreduce-examples.jar**. В качестве входных данных он использует документ **wasb://example/data/gutenberg/davinci.txt**, а выходные данные сохраняются в **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Дополнительные сведения об этом задании MapReduce и данные для примера см. в разделе <a href="hdinsight-use-mapreduce.md">Использование MapReduce в HDInsight в Hadoop</a>.

2. Задание выдает информацию о ходе обработки, а по завершении задания возвращает информацию, аналогичную приведенной ниже:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. По завершении задания воспользуйтесь следующей командой, чтобы вывести список выходных файлов, хранящихся в **wasb://example/data/WordCountOutput**.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Должно отобразиться два файла, **\_SUCCESS** и **part-r-00000**. Файл **part-r-00000** содержит выходные данные этого задания.

	> [AZURE.NOTE] Некоторые задания MapReduce могут разделять результаты на несколько файлов **part-r-№№№№№**. В этом случае используйте суффикс №№№№№, чтобы определить порядок файлов.

4. Чтобы просмотреть выходные данные, используйте следующую команду:

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Отобразится список слов, которые содержатся в файле **wasb://example/data/gutenberg/davinci.txt**, а также количество вхождений каждого слова. Ниже приведен пример данных, которые будут содержаться в файле.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Сводка

Как видите, команда Hadoop позволяет с легкостью выполнять задания MapReduce в кластере HDInsight и просматривать выходные данные задания.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0427_2016-->