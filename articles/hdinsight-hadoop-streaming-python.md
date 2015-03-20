<properties
   pageTitle="Разработка заданий Python MapReduce с HDInsight | Azure"
   description="Информация о создании и выполнении заданий Python MapReduce в кластерах HDInsight на платформе Linux."
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

#Разработка программ потоковой передачи на Python для HDInsight

Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. В этом документе вы узнаете, как использовать Python для выполнения операций MapReduce.

> [AZURE.NOTE] Эта статья основана на информации и примерах, опубликованных Майклом Ноллом (Michael Noll) на странице [http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##Требования

* Hadoop в кластере HDInsight на платформе Linux

* Текстовый редактор

* Для клиентов Windows, PuTTY и PSCP. Эти служебные программы доступны по адресу:<a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

##Статистика

В этом примере будет реализована базовая возможность подсчета статистики по словам с помощью средств сопоставления и приведения. Средство сопоставления будет делить предложение на отдельные слова, а средство приведения - вычислять количество слов для вывода результата.

Ниже показано, что происходит на этапах сопоставления и приведения.

![illustration of map reduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Почему Python?

Python - это высокоуровневый язык программирования общего назначения, позволяющий выразить основные понятия с помощью меньшего числа строк кода, чем это можно сделать на многих других языках. Недавно он стал довольно популярным благодаря специалистам по обработке и анализу данных, которые используют его в качестве языка для создания прототипов. Благодаря своему интерпретируемому характеру, динамической типизации и изящному синтаксису он прекрасно подходит для быстрой разработки приложений.

Python устанавливается на всех кластерах HDInsight.

##Потоковая передача MapReduce

Hadoop позволяет указать файл, содержащий логику сопоставления и приведения, используемую для выполнения задания. К сопоставлению и приведению выдвигаются определенные требования:

* **Входные данные** - компоненты сопоставления и приведения должны считывать входные данные с STDIN.

* **Выходные данные** - компоненты сопоставления и приведения должны записывать выходные данные на STDOUT.

* **Формат данных** - полученные и созданные данные должны быть парой "ключ-значение", разделенной символом табуляции.

Python позволяет с легкостью выполнить эти требования с помощью модуля **sys** для чтения с STDIN и **print** для вывода на STDOUT. Остается просто отформатировать данные, вставив символ табуляции (`\t`) между ключом и значением.

##Создание средств сопоставления и приведения

Средства сопоставления и приведения - это обычные текстовые файлы. В данном случае **mapper.py** - это средство сопоставления, а **reducer.py** - средство приведения. Для их создания можете воспользоваться любым текстовым редактором по своему усмотрению.

###Mapper.py

Создайте новый файл с именем **mapper.py**, и в качестве его содержимого используйте следующий текст.

	#!/usr/bin/env python

	# Use the sys module
	import sys

	# 'file' in this case is STDIN
	def read_input(file):
		# Split each line into words
		for line in file:
			yield line.split()

	def main(separator='\t'):
		# Read the data using read_input
		data = read_input(sys.stdin)
		# Process each words returned from read_input
		for words in data:
			# Process each word
			for word in words:
				# Write to STDOUT
				print '%s%s%d' % (word, separator, 1)

	if __name__ == "__main__":
		main()

Просмотрите код, чтобы понять, что он делает.

###Reducer.py

Создайте новый файл с именем **reducer.py**, и в качестве его содержимого используйте следующий текст.

	#!/usr/bin/env python
	
	# import modules
	from itertools import groupby
	from operator import itemgetter
	import sys
	
	# 'file' in this case is STDIN
	def read_mapper_output(file, separator='\t'):
		# Go through each line
	    for line in file:
			# Strip out the separator character
	        yield line.rstrip().split(separator, 1)
	
	def main(separator='\t'):
	    # Read the data using read_mapper_output
	    data = read_mapper_output(sys.stdin, separator=separator)
		# Group words and counts into 'group'
		#   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
	    for current_word, group in groupby(data, itemgetter(0)):
	        try:
				# For each word, pull the count(s) for the word
				#   from 'group' and create a total count
	            total_count = sum(int(count) for current_word, count in group)
				# Write to stdout
	            print "%s%s%d" % (current_word, separator, total_count)
	        except ValueError:
	            # Count was not a number, so do nothing
	            pass
	
	if __name__ == "__main__":
	    main()

##Передача файлов

Для запуска файлов **mapper.py** и **reducer.py** оба они должны быть на головном узле кластера. Проще всего будет передать их с помощью **scp** (**pscp** при использовании клиента Windows).

Используйте на клиенте следующую команду в том же каталоге, в котором расположены файлы **mapper.py** и **reducer.py**. Замените **username** именем пользователя SSH, а **clustername** - именем кластера.

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

В результате файлы будут скопированы из локальной системы на головной узел.

> [AZURE.NOTE] Если для защиты учетной записи SSH использовался пароль, будет предложено ввести пароль. Если использовался ключ SSH, возможно нужно будет использовать параметр `-i` и указать путь к закрытому ключу. Например, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

##Запуск MapReduce

1. Подключитесь к кластеру с помощью SSH.

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE] Если для защиты учетной записи SSH использовался пароль, будет предложено ввести пароль. Если использовался ключ SSH, возможно нужно будет использовать параметр `-i` и указать путь к закрытому ключу. Например, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

2. Используйте следующую команду для запуска задания MapReduce.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/davinci.txt -output wasb:///example/wordcountout

	Эта команда состоит из следующих частей:

	* **hadoop streaming.jar** - используется при выполнении операций потоковой передачи MapReduce. Эта часть обеспечивает взаимодействие Hadoop с предоставленным внешним кодом MapReduce.
	
	* **-files** - сообщает Hadoop, что для этого задания MapReduce необходимы указанные файлы, которые нужно скопировать на все рабочие узлы

	* **-mapper** - сообщает Hadoop о том, какой файл следует использовать в качестве средства сопоставления.

	* **-reducer** - сообщает Hadoop о том, какой файл следует использовать в качестве средства приведения.

	* **-input** - входной файл, в котором нужно подсчитать статистику.

	* **-output** - каталог, в который будут записаны выходные данные.

		> [AZURE.NOTE] Этот каталог будет создан при выполнении задания.

При запуске задания появится много операторов **INFO**, а в конце будет отображаться процент выполнения операций **map** и **reduce**.

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

После завершения задания вы получите информацию о его состоянии.

##Просмотр результатов

По завершении задания воспользуйтесь следующей командой, чтобы просмотреть выходные данные.

	hadoop fs -text /example/wordcountout/part-00000

После этого должен отобразиться список слов и частота их встречаемости. Ниже приведен пример выходных данных.

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##Дальнейшие действия

Теперь, когда вы узнали, как использовать задания потоковой передачи MapReduce в HDInsight, воспользуйтесь ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Использование Hive с HDInsight](../hdinsight-use-hive/)
* [Использование Pig с HDInsight](../hdinsight-use-pig/)
* [Использование заданий MapReduce с HDInsight](../hdinsight-use-mapreduce)
<!--HONumber=47-->
