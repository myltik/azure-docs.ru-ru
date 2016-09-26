<properties
   pageTitle="Использование MapReduce и подключения SSH с Hadoop в HDInsight | Microsoft Azure"
   description="Информация об использовании SSH для выполнения заданий MapReduce с помощью Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# Использование MapReduce с Hadoop в HDInsight с помощью SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этой статье вы узнаете, как использовать протокол SSH для подключения к Hadoop в кластере HDInsight и последующей отправки заданий MapReduce с помощью команд Hadoop.

> [AZURE.NOTE] Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, см. раздел [Советы по использованию HDInsight на платформе Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight на основе Linux (Hadoop в HDInsight).

* Клиент SSH. Клиент SSH должен входить в состав операционных систем Linux, Unix и Mac OS. Пользователи Windows должны загрузить отдельный клиент, например [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Подключение по SSH

Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя — это имя, присвоенное кластеру, заканчивающееся на **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для аутентификации SSH**, возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе. Пример:

	ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

Если при создании кластера HDInsight **для аутентификации SSH был задан пароль**, при появлении запроса необходимо будет ввести пароль.

Дополнительную информацию об использовании SSH с HDInsight см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (клиенты Windows)

Windows не предоставляет встроенный клиент SSH. Рекомендуется использовать **PuTTY**, который можно скачать по адресу: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Дополнительные сведения об использовании PuTTY см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Использование команд Hadoop

1. После подключения к кластеру HDInsight используйте следующую команду **Hadoop**, чтобы запустить задание MapReduce:

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	При этом запустится класс **wordcount**, содержащийся в файле **hadoop-mapreduce-examples.jar**. В качестве входных данных он использует документ **wasbs://example/data/gutenberg/davinci.txt**, а выходные данные сохраняются здесь: **wasbs:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Дополнительную информацию об этом задании MapReduce и примере данных см. в статье [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md).

2. Задание выдает информацию о ходе обработки, а по завершении задания возвращает информацию, аналогичную приведенной ниже:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. По завершении задания воспользуйтесь следующей командой, чтобы вывести список выходных файлов, хранящихся в **wasbs://example/data/WordCountOutput**.

		hdfs dfs -ls wasbs:///example/data/WordCountOutput

	Должно отобразиться два файла: **\_SUCCESS** и **part-r-00000**. Файл **part-r-00000** содержит выходные данные этого задания.

	> [AZURE.NOTE] Некоторые задания MapReduce могут разделять результаты на несколько файлов **part-r-№№№№№**. В этом случае используйте суффикс №№№№№, чтобы определить порядок файлов.

4. Чтобы просмотреть выходные данные, используйте следующую команду:

		hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

	Отобразится список слов, которые содержатся в файле **wasbs://example/data/gutenberg/davinci.txt**, а также количество вхождений каждого слова. Ниже приведен пример данных, которые будут содержаться в файле.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Сводка

Как видите, команды Hadoop позволяют с легкостью выполнять задания MapReduce в кластере HDInsight и просматривать выходные данные задания.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0914_2016-->