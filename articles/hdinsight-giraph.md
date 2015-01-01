<properties title="How to use Giraph with HDInsight" pageTitle="Как использовать Apache Giraph с Azure HDInsight" description="Узнайте, как использовать Apache Giraph для обработки графов с помощью Azure HDInsight" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

#Использование Apache Giraph с Azure HDInsight (Hadoop)

[Apache Giraph][giraph] позволяет выполнять обработку графов с использованием Hadoop, а также может использоваться с Azure HDInsight. 

Графы моделируют взаимоотношения между объектами, таки как подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях (иногда называется социальным графом.) Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

* определение потенциальных друзей на основе текущих взаимоотношений

* определение кратчайшего маршрута между двумя компьютерами в сети

* вычисление ранга страницы веб-страниц

##Вы научитесь:

* [Создавать и развертывать Apache Giraph в кластере HDInsight](#build)

* [Запускать пример SimpleShortestPathsComputation](#run)

	Полный список примеров, предоставленных вместе с Giraph, см. в [пакете org.apache.giraph.examples](https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html).

* [Устранять возникающие проблемы](#tshoot)

##Требования

* Кластер Azure HDInsight версии 3.0 или 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 или больше

##<a id="build"></a>Создание и развертывание Giraph

Giraph не предоставляется как часть кластера HDInsight и должен быть создан из источника.  Более подробную информацию о создании Giraph см. в [репозитории Giraph](https://github.com/apache/giraph).

1. В настоящее время (14-07-2014,) Giraph требует исправлений для работы с хранилищем файлов WASB, которое используется в HDInsight. Исправление представлено в проект Apache Giraph, но еще не принято. Скачайте исправление из раздела "attachments" [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930) и сохраните его на локальном диске как giraph-930.diff.

1. В командой строке введите следующую команду Git, чтобы создать клон репозитория Giraph.

		git clone https://github.com/apache/giraph.git

2. Измените каталоги на каталог giraph, созданный при выполнении операции клонирования на шаге 2.

		cd giraph

3. Объедините исправление с локальным репозиторием, используя следующую команду.

		git apply giraph-930.diff

	Замените giraph-930.diff на путь к файлу, который вы создали на шаге 1.

3. Создайте репозиторий Giraph для своей версии кластера HDInsight, используя одну из следующих команд.

	* Для HDInsight 3.0 (Hadoop 2.2)

			mvn package -Phadoop_0.20.203 - DskipTests

	* Для HDInsight 3.1 (Hadoop 2.4)

			mvn package -Phadoop_0.23 -DskipTests

	После завершения сборки JAR-файл с примерами можно найти здесь: \\giraph\\giraph-examples\\target.

4. Отправьте JAR-файл с примером в первичное хранилище кластера HDInsight с помощью [Azure PowerShell][aps] и [инструментов HDInsight][tools].

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	Замените giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar на путь и имя JAR-файла, созданного на предыдущем шаге, и выполните команду clustername с использованием имени кластера HDInsight. Например, если создан пакет с параметром -Phadoop_0.20.203, имя JAR-файла будет содержать hadoop-0.20.203.

	После выполнения команды JAR-файл будет отправлен в wasb:///example/jars/giraph.jar.

	> [WACOM.NOTE] Список служебных программ для отправки файлов в HDInsight см. в разделе [Отправка данных для заданий Hadoop в HDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-upload-data/).

##<a id="run"></a>Запуск примера

Пример SimpleShortestPathsComputation демонстрирует базовую реализацию [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) для поиска кратчайшего пути между объектами в графе. Выполните следующие действия, чтобы отправить данные примера, запустить задание с использованием примера SimpleShortestPathsComputation, а затем просмотреть результаты.

> [WACOM.NOTE] Источник для этого и других примеров доступен в [ветви release-1.1](https://github.com/apache/giraph/tree/release-1.1)  [репозитория GitHub](https://github.com/apache/giraph).

1. Создайте новый файл с именем tiny\_graph.txt. В нем должны присутствовать следующие строки.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Эти данные описывают взаимоотношения между объектами в [направленном графе](http://en.wikipedia.org/wiki/Directed_graph) с использованием формата [source_id,source_value,[[dest_id], [edge_value],...]]. Каждая строка представляет взаимоотношение между объектом source\_id и одним или несколькими объектами dest\_id. Значение edge\_value (или вес) можно представить себе как силу или расстояние соединения между source\_id и dest\_id. 

	В начерченном виде и с использованием значения (или веса) в качестве расстояния между объектами указанные выше данные могут выглядеть следующим образом.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. Отправьте файл tiny\_graph.txt в первичное хранилище кластера HDInsight с помощью [Azure PowerShell][aps] и [инструментов HDInsight][tools].

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	Замените имя кластера на имя кластера HDInsight.

3. Используйте следующую команду PowerShell для запуска примера SimpleShortstPathsComputation с файлом tiny\_graph.txt в качестве входных данных. Для выполнения необходимо установить и настроить [Azure PowerShell][aps].

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
		                "-ca", "mapred.job.tracker=headnodehost:9010", `
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
		                "-vip", "wasb:///example/data/tinygraph.txt", `
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
		                "-op",  "wasb:///example/output/shortestpaths", `
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		  -JarFile $jarFile `
		  -ClassName "org.apache.giraph.GiraphRunner" `
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	В приведенном выше примере замените clustername именем кластера HDInsight.

###Просмотр результатов

После завершения задания результаты будут храниться в папке wasb:///example/out/shotestpaths как файлы part-m-#####. Используйте [Azure PowerShell][aps] и [инструменты HDInsight][tools], чтобы скачать выходные файлы.

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

В результате создается структура каталога example/output/shortestpaths в текущем каталоге и скачиваются файлы, начинающиеся с part. Командлет Cat затем отображает содержимое файлов, которое должно выглядеть следующим образом.

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Таким образом, выход должен считываться как "расстояние destination_id", где расстояние представляет собой значение (или вес) переходов на пути между объектом ID 1 и целевым объектом ID.

При визуализации можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Обратите внимание, что кратчайший путь между ID 1 и ID 4 - это 5. Это общее расстояние между <span style="color:orange">ID 1 и 3</span>, а затем <span style="color:red">ID 3 и 4</span>.

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>Устранение неполадок

###Выходной каталог уже существует

Задания Giraph создают определенный выходной каталог во время выполнения. Если каталог уже существует, возникает ошибка, указывающая, что выходной каталог уже существует.

При необходимости запустить задание несколько раз необходимо либо удалять выходной каталог между заданиями, либо задавать различные выходные каталоги для каждого задания.

###<a id="cmd"></a>Использование командной строки Hadoop.

Хотя в это статье демонстрируется запуск задания Giraph через PowerShell, можно также запустить это задание с помощью командной строки Hadoop.

> [WACOM.NOTE] Командная строка Hadoop доступна только при подключении кластера HDInsight с использованием удаленного рабочего стола.
> 
> Сеансы удаленного рабочего стола к вычислительным ресурсам Azure, таким как кластер HDInsight, могут только работать из клиентов удаленных рабочих столов под управлением Windows.

Чтобы подключиться к кластеру HDInsight, выполните следующие действия:

1. С помощью [портала управления Azure](https://manage.windowsazure.com) выберите свой кластер HDInsight, а затем выберите "Конфигурация".

2. В нижней части страницы выберите "Включить удаленный доступ" и введите имя пользователя, пароль и дату окончания срока действия для подключения удаленного рабочего стола.

3. После обработки запроса на включение удаленного рабочего стола появляется новая запись в разделе "Подключения" в нижней части страницы. Выберите ее, чтобы загрузить файл .RDP для сеанса удаленного рабочего стола.

4. Файл .RDP можно сохранить или немедленно открыть, чтобы запустить клиента удаленного рабочего стола. Во время процесса подключения поступит запрос на ввод имени пользователя и пароля, которые используются для включения подключения удаленного рабочего стола.

5. После подключения используйте значок командной строки Hadoop на рабочем столе, чтобы запустить командную строку Hadoop.

6. В следующем примере показано, как скопировать файл giraph.jar в головной узел кластера, а затем запускать задание с использованием командной строки Hadoop.

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###Более старые версии HDInsight

Если потребуется использовать Giraph с более старыми версиями HDInsight, необходимо обеспечить ее соответствие с определенной версией Hadoop, которая поддерживается данной версией. См. раздел [Новые возможности в версиях кластеров HDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/) , чтобы определить версию Hadoop, которая соответствует вашей версии HDInsight.

Кроме того, более старые версии HDInsight могут потребовать, чтобы задание Giraph запускалось из командной строки Hadoop. При возникновении ошибок во время запуска задания из PowerShell попробуйте его запустить из [командной строки Hadoop](#cmd).

##Дальнейшие действия

Теперь, после того, как вы научились использовать Giraph с HDInsight, попробуйте использовать [Pig][] и [Hive][] с HDInsight.

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-use-hive/

<!--HONumber=35_1-->
