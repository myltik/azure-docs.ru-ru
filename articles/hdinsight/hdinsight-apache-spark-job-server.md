<properties 
	pageTitle="Сервер заданий Apache Spark в HDInsight | Microsoft Azure" 
	description="Узнайте о том, как использовать сервер заданий Spark для удаленной отправки заданий в кластер Spark и управления ими." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Сервер заданий Spark в кластерах Azure HDInsight (Windows)

> [AZURE.NOTE] Теперь HDInsight предоставляет кластеры Spark в Linux; для удаленной отправки заданий в кластер Spark используется Livy. Сведения о том, как использовать Livy с кластерами HDInsight Spark в Linux, см. в разделе [Удаленная отправка заданий Spark с помощью Livy в кластерах Spark в HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md).

В рамках развертывания кластера Apache Spark в Azure HDInight устанавливается сервер заданий Spark. Сервер заданий Spark предоставляет интерфейсы API REST для создания контекста Spark, отправки приложений Spark в контекст, проверки состояния выполнения задания, удаления контекста и т. д. В этой статье приведены несколько примеров использования Curl для выполнения некоторых распространенных задач в кластере Spark с использованием сервера заданий.

>[AZURE.NOTE] Полная документация по серверу заданий Spark представлена на сайте по адресу [https://github.com/spark-jobserver/spark-jobserver](https://github.com/spark-jobserver/spark-jobserver).

## <a name="uploadjar"></a>Отправка JAR-файла в кластер Spark

	curl.exe -k -u "<hdinsight user>:<user password>" --data-binary @<location of jar on the computer> https://<cluster name>.azurehdinsight.net/sparkjobserver/jars/<application name>

Пример:
	
	curl.exe -k -u "myuser:myPass@word1" --data-binary @C:\mylocation\eventhubs-examples\target\spark-streaming-eventhubs-example-0.1.0-jar-with-dependencies.jar https://mysparkcluster.azurehdinsight.net/sparkjobserver/jars/streamingjar


##<a name="createcontext"></a>Создание нового постоянного контекста на сервере заданий

	curl.exe -k -u "<hdinsight user>:<user password>" -d "" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context name>?num-cpu-cores=<value>&memory-per-node=<value>"

Пример:

	curl.exe -k -u "myuser:myPass@word1" -d "" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming?num-cpu-cores=4&memory-per-node=1024m"


##<a name="submitapp"></a>Отправка приложения в кластер

	curl.exe -k -u "<hdinsight user>:<user password>" -d @<input file name> "https://<cluster name>.azurehdinsight.net/sparkjobserver/jobs?appName=<app name>&classPath=<class path>&context=<context>"

Пример:

	curl.exe -k -u "myuser:myPass@word1" -d @mypostdata.txt "https://mysparkcluster.azurehdinsight.net/sparkjobserver/jobs?appName=streamingjar&classPath=org.apache.spark.streaming.eventhubs.example.EventCountJobServer&context=mystreaming"

где mypostdata.txt определяет приложение.


##<a name="submitapp"></a>Удаление задания

	curl.exe -X DELETE -k -u "<hdinsight user>:<user password>" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context>"

Пример:

	curl.exe -X DELETE -k -u "myuser:myPass@word1" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming"


##<a name="seealso"></a>См. также:

* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Создание Spark в кластере HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->