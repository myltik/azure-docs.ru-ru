<properties
   pageTitle="MapReduce с Hadoop в HDInsight | Azure"
   description="Информация об использовании SSH для выполнения заданий MapReduce с помощью Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Использование Hive с Hadoop в HDInsight с применением протокола SSH

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

В этой статье вы узнаете, как использовать SSH для подключения к Hadoop в кластере HDInsight и последующей отправки заданий MapReduce с помощью команды Hadoop.

> [AZURE.NOTE] Если вы уже знакомы с использованием серверов под управлением Linux Hadoop, но не знакомы с HDInsight, см. раздел <a href="../hdinsight-hadoop-linux-information/" target="_blank">Что необходимо знать о Hadoop в HDInsight на основе Linux</a>.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight на основе Linux (Hadoop в HDInsight).

* Клиент SSH. Клиент SSH должен входить в состав Linux, Unix и Mac OS. Пользователи Windows должны загрузить клиент, например, <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Подключение с SSH

Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя - это имя, присвоенное кластеру, с суффиксом **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для аутентификации SSH**, возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Если при создании кластера HDInsight для аутентификации SSH был задан пароль**, при появлении запроса необходимо будет ввести пароль.

###PuTTY (клиенты Windows)

Windows не предоставляет встроенный клиент SSH. Рекомендуется использовать **Putty**, который можно загрузить по адресу <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Дополнительные сведения об использовании Putty см. в подразделе **Использование Putty для подключения к компьютеру под управлением Linux** раздела <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Использование SSH с Linux в Azure</a>.

> [AZURE.NOTE] Если сертификат используется для проверки подлинности SSH для кластера HDInsight, также необходимо ознакомиться с подразделом **Создание PPK для Putty** раздела <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Использование SSH с Linux в Azure</a>

##<a id="hadoop"></a>Использование команды Hadoop

1. После подключения к кластеру HDInsight используйте следующую команду, чтобы с помощью команды **Hadoop** запустить задание MapReduce.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	При этом запустится класс **wordcount**, содержащийся в файле **hadoop-mapreduce-examples.jar**. В качестве входных данных он использует документ **wasb://example/data/gutenberg/davinci.txt**, а выходные данные сохраняются в **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Дополнительные сведения об этом задании MapReduce и данные для примера см. в разделе <a href="../hdinsight-use-mapreduce/" target="_blank">Использование MapReduce в HDInsight Hadoop</a>.

2. Задание будет выдавать информацию о ходе обработки, а по завершении задания вернет информацию, аналогичную приведенной ниже.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. После завершения используйте следующую команду, чтобы вывести список выходных файлов, сохраненных в **wasb://example/data/WordCountOutput**.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Должно появиться два файла, **_SUCCESS** и **part-r-00000**. Файл **part-r-00000** содержит выходные данные этого задания.

	> [AZURE.NOTE] Некоторые задания MapReduce могут разделять результаты на несколько файлов **part-r-№№№№№**. В этом случае используйте суффикс №№№№№, чтобы определить порядок файлов.

4. Чтобы просмотреть выходные данные, используйте следующую команду.

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Она отобразит список слов, содержащихся в файле **wasb://example/data/gutenberg/davinci.txt**, а также количество вхождений каждого из них.  Ниже приведен пример данных, которые будут содержаться в файле.

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

Вот где можно ознакомиться с общей информацией о заданиях MapReduce в HDInsight.

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
<!--HONumber=47-->
