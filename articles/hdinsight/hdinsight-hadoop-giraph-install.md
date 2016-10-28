<properties
	pageTitle="Установка и использование Giraph для кластеров Hadoop в HDInsight | Microsoft Azure"
	description="Дополнительные сведения о настройке кластера HDInsight с Giraph и использование Giraph."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# Установка и использование Giraph в HDInsight


Научитесь настраивать кластер HDInsight на основе Windows с Giraph с помощью сценария действия и использовать Giraph для обработки диаграмм больших объемов. Сведения об использовании Giraph с кластером под управлением Linux см. в разделе [Установка Giraph в кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Giraph можно установить в кластере любого типа (Hadoop, Storm, HBase, Spark) в HDInsight в Azure, воспользовавшись *Действием сценария*. Пример сценария для установки Giraph в кластере HDInsight доступен в большом двоичном объекте службы хранилища Azure (доступ только для чтения): [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Пример скрипта работает только с кластером HDInsight версии 3.1. Дополнительную информацию о версиях кластера HDInsight см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).

**Связанные статьи**

- [Установка Giraph на кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md). Общие сведения о создании кластеров HDInsight
- [Настройка кластеров HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария
- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md)

## Что такое Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight. Графы моделируют взаимоотношения между объектами, такие как подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях (иногда называется социальным графом). Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

- определение потенциальных друзей на основе текущих взаимоотношений;
- определение кратчайшего маршрута между двумя компьютерами в сети;
- вычисление ранга страницы для веб-страниц.


## Установка Giraph с помощью портала

1. Начните создание кластера с помощью параметра **НАСТРАИВАЕМОЕ СОЗДАНИЕ**, как описано в разделе [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md#portal).
2. На странице **Действия сценариев** мастера щелкните **Добавить действие сценария** для предоставления информации о данном действии сценария, как показано ниже:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Использование действия сценария для настройки кластера")

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия сценария. Например, <b>Установка Giraph</b>.</td></tr>
		<tr><td>URI-адрес сценария</td>
			<td>Укажите универсальный идентификатор ресурса (URI) для сценария, который вызывается для настройки кластера. Например, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i>.</td></tr>
		<tr><td>Тип узла</td>
			<td>Укажите узлы, на которых выполняется сценарий настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите параметры, если они требуются для сценария. Скрипт для установки Giraph не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
	</table>

	Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните флажок, чтобы начать создание кластера.

## Использование Giraph

Мы используем пример SimpleShortestPathsComputation, который демонстрирует базовую реализацию <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> для поиска кратчайшего пути между объектами графа. Выполните следующие действия, чтобы отправить данные и JAR-файл примера, запустить задание с использованием примера SimpleShortestPathsComputation, а затем просмотреть результаты.

1. Передайте образец файла данных в хранилище BLOB-объектов Azure. На локальной рабочей станции создайте новый файл с именем **tiny\_graph.txt**. В нем должны присутствовать следующие строки.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Отправьте файл tiny\_graph.txt в основное хранилище для кластера HDInsight. Указания по отправке данных см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).

	Эти данные описывают взаимоотношения между объектами в направленном графе с использованием формата [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Каждая строка представляет взаимоотношение между объектом **source\\_id** и одним или несколькими объектами **dest\\_id**. Значение **edge\\_value** (или вес) можно представить себе как силу или расстояние подключения между **source\_id** и **dest\\_id**.

	В визуальной форме и с использованием значения (или веса) в качестве расстояния между объектами указанные выше данные могут выглядеть следующим образом.

	![tiny\_graph.txt начерчен в виде кругов с линиями различной длины между](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Запустите пример SimpleShortestPathsComputation. Воспользуйтесь следующими командлетами Azure PowerShell для запуска этого примера, используя файл tiny\_graph.txt в качестве входных данных.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasbs:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasbs:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasbs:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments

		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	В приведенном выше примере замените **clustername** именем кластера HDInsight, где установлен Giraph.

5. Просмотрите результаты. После завершения задания результаты будут сохранены в двух выходных файлах в папке __wasbs:///example/out/shotestpaths__. Эти файлы называются __part-m-00001__ и __part-m-00002\_\_. Выполните следующие действия, чтобы скачать и просмотреть выходные данные.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	В результате создается структура каталога __example/output/shortestpaths__ в текущем каталоге на рабочей станции, а выходные файлы скачиваются в это расположение.

	Используйте командлет __Cat__, чтобы отобразить содержимое файлов:

		Cat example/output/shortestpaths/part*

	Результат должен выглядеть аналогично следующему:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Таким образом, выходные данные должны считываться как `destination_id distance`, где расстояние представляет собой значение (или вес) переходов на пути между объектом ID 1 и целевым объектом ID.

	При визуализации можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Обратите внимание, что кратчайший путь между ID 1 и ID 4 — это 5. Это общее расстояние между объектами <span style="color:orange">ID 1 и ID 3</span> и между объектами <span style="color:red">ID 3 и ID 4</span>.

	![Представление объектов в виде кругов с кратчайшими путями между ними](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## Установка Giraph с помощью Azure PowerShell

Обратитесь к статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). В этом примере показано, как установить Spark с помощью Azure PowerShell. Необходимо изменить сценарий для использования [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## Установка Giraph с помощью пакета SDK для .NET

Обратитесь к статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). В этом примере показано, как установить Spark с помощью пакета SDK для .NET. Необходимо изменить сценарий для использования [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## Дополнительные материалы

- [Установка Giraph на кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md). Общие сведения о создании кластеров HDInsight
- [Настройка кластеров HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария
- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md)
- [Установка и использование Spark в HDInsight][hdinsight-install-spark]. Пример действия сценария для установки Spark
- [Установка R в кластерах HDInsight][hdinsight-install-r]. Пример действия сценария для установки R
- [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). Пример действия сценария для установки Solr



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0914_2016-->