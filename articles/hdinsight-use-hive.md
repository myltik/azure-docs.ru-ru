<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Использование Hadoop Hive в HDInsight для платформы Azure" metaKeywords="" description="Вы узнаете, как использовать Hive с HDInsight. Файл журнала будет использоваться в качестве входных данных в таблице HDInsight, а HiveQL будет использоваться для запроса данных и подготовки отчета по базовой статистике." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Использование Hive с Hadoop в HDInsight

[Apache Hive][apache-hive] предоставляет средства выполнения задания MapReduce с помощью языка сценариев, напоминающего SQL, который называется HiveQL. Hive - это система хранилища данных для Hadoop, которая позволяет производить обобщение и анализ данных большого размера, а также создавать запросы для этих данных. В этой статье вы будете использовать HiveQL для создания запросов на основе образцов данных, которые устанавливаются вместе с распределением кластеров.


**Предварительные требования:**

- Вы должны были подготовить кластер **HDInsight**. Пошаговое руководство по выполнению этой задачи с помощью портала Azure см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started]. Дополнительную информацию о различных способах создания таких кластеров см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision].

- Среда **Azure PowerShell** должна быть установлена на рабочей станции. Указания по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

##Содержание

* [Пример использования Hive](#usage)
* [Передача данных для таблиц Hive](#uploaddata)
* [Выполнение запросов Hive с помощью PowerShell](#runhivequeries)
* [Выполнение запросов Hive с помощью инструментов HDInsight для Visual Studio](#runhivefromvs)
* [Для лучшей производительности используйте Tez](#usetez)
* [Дальнейшие действия](#nextsteps)

##<a id="usage"></a>Пример использования Hive

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Проекты Hive структурируют большой объем неструктурированных данных и затем позволяют вам выполнять запросы к данным. Hive обеспечивает необходимый уровень абстрагирования при работе с платформой приложения MapReduce на базе Java, позволяя пользователям, не владеющим Java и MapReduce, создавать запросы для работы с данными. HiveQL (язык запросов Hive) позволяет создавать запросы, используя операторы, подобные операторам T-SQL. HDInsight автоматически компилирует запросы HiveQL в MapReduce и выполняет их в кластере. Другие преимущества Hive:

- Hive позволяет программистам, знакомым с платформой MapReduce, подключать пользовательские программы map и reduce для выполнения более сложного анализа, который может не поддерживаться встроенными возможностями языка HiveQL.
- Hive лучше всего подходит для пакетной обработки больших объемов неизменяемых данных (например, веб-журналов). Но не подходит для приложений обработки транзакций, которым требуется очень маленькое время отклика, например систем управления базами данных.
- Система Hive оптимизирована для масштабируемости (можно динамически добавлять компьютеры в кластере Hadoop), расширяемости (в структуре MapReduce и с другими интерфейсами программирования) и отказоустойчивости. Задержка не является ключевым вопросом разработки.

##<a id="uploaddata"></a>Передача данных для таблиц Hive

HDInsight использует в качестве файловой системы по умолчанию хранилище больших двоичных объектов Azure для кластеров Hadoop. Во время распределения кластеров несколько файлов-примеров с данными были добавлены в хранилище BLOB-объектов. В статье используется файл примера log4j, который устанавливается вместе с кластерами HDInsight и размещается в каталоге **/example/data/sample.log** под вашим контейнером больших двоичных объектов. Каждый журнал в файле состоит из строки полей, содержащей поле [УРОВЕНЬ ЖУРНАЛА] для отображения типа и серьезности. Например:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

В этом примере уровень журнала имеет значение "ОШИБКА".

> [AZURE.NOTE] У вас также есть возможность создавать свои собственные файлы log4j при помощи служебной программы входа в систему [Apache Log4j][apache-log4j], а затем передавать файлы в контейнер больших двоичных объектов. Дополнительную информацию см. в разделе [Отправка данных в HDInsight][hdinsight-upload-data]. Дополнительную информацию о том, каким образом хранилище больших двоичных объектов Azure используется с HDInsight, см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

HDInsight может обращаться к файлам хранилища больших двоичных объектов с помощью префикса **wasb**. Поэтому для доступа к файлу sample.log необходимо использовать следующий синтаксис:

	wasb:///example/data/sample.log

Так как WASB является хранилищем по умолчанию для HDInsight, вы можете обратиться к файлу **/example/data/sample.log**.

> [AZURE.NOTE] Вышеописанный синтаксис **wasb:///** используется для доступа к файлам, которые хранятся в контейнере хранилища по умолчанию для вашего кластера HDInsight. Если вы указали дополнительные учетные записи хранения при подготовке кластера и хотите обращаться к файлам из этих учетных записей, это можно сделать, указав имя контейнера и адрес учетной записи хранения. Например: **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="runhivequeries"></a> Выполнение запросов Hive с помощью PowerShell

Запросы Hive можно выполнять в PowerShell с помощью командлета **Start-AzureHDInsightJob** или **Invoke-Hive**.

* **Start-AzureHDInsightJob** является командлетом запуска заданий, который используется для запуска заданий Hive, Pig и MapReduce в кластере HDInsight cluster. **Start-AzureHDInsightJob** является асинхронным командлетом, который завершается до окончания задания. При завершении он возвращает информацию о задании и может использоваться вместе с такими командлетами как **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob** и **Get-AzureHDInsightJobOutput**.  **Get-AzureHDInsightJobOutput** используется для получения информации, которую задание записывает в поток **STDOUT** или **STDERR**.

* **Invoke-Hive** выполняет запрос Hive, ожидает его завершения и получает выходные данные запроса единым действием.

1. Откройте окна консоли Azure PowerShell. Дополнительную информацию см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

2. Задайте переменные в следующем сценарии, а затем запустите его.

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Выполните следующий сценарий, чтобы создать новую таблицу с именем **log4jLogs** и демонстрационными данными.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	Запрос HiveQL выполняет следующие действия:

	* **DROP TABLE** - приводит к удалению таблицы и файла данных в том случае, если таблица уже существует.
	* **CREATE EXTERNAL TABLE** - создает новую внешнюю таблицу в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.
	* **ROW FORMAT** - указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
	* **STORED AS TEXTFILE LOCATION** - указывает Hive место, где хранятся данные (например, в каталоге example/data), а также то, что они хранятся в виде текста.
	* **SELECT** - подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Будет возвращено значение **3**, так как таблица содержит три строки с данным значением.

	> [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
	>
	> Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только описание таблицы.


4. Выполните следующий сценарий, чтобы создать определение задания Hive из предыдущего запроса:

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Можно также использовать параметр -File, чтобы указать файл сценария HiveQL в HDFS.

5. Выполните следующий сценарий, чтобы отправить задание Hive:

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. Выполните следующий сценарий, чтобы дождаться завершения задания Hive:

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. Выполните следующий сценарий, чтобы напечатать стандартный вывод:

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	Результат:

		[ERROR] 3

	означает, что в файле sample.log было обнаружено три случая с записью "ERROR".

4. Чтобы использовать **Invoke-Hive**, сначала необходимо выбрать используемый кластер.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. Используйте следующий сценарий, чтобы создать новую "внутреннюю" таблицу с именем **errorLogs** с помощью командлета **Invoke-Hive**.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	Данная операция выполняет следующие действия:

	* **CREATE TABLE IF NOT EXISTS** - будет создана таблица, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет являться "внутренней", то есть храниться в хранилище данных Hive и полностью обслуживаться Hive.
	* **STORED AS ORC** - данные будут храниться в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
	* **INSERT OVERWRITE ... SELECT** - из таблицы **log4jLogs** будут выбраны строки, которые содержат значение **[ERROR]**, а затем данные будут вставлены в таблицу **errorLogs**.

	> [AZURE.NOTE] В отличие от внешних (**EXTERNAL**) таблиц, удаление внутренних таблиц приводит также к удалению данных.

	Выходные данные будут выглядеть следующим образом:

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] Для более длинных запросов HiveQL вы можете использовать PowerShell Here-Strings или файл сценария HiveQL. В приведенном ниже отрывке кода показано, как использовать командлет Invoke-Hive для запуска файла сценария HiveQL. Файл сценария HiveQL необходимо передать в WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Дополнительную информацию о конструкции Here-Strings см. в разделе [Использование конструкции Here-Strings в Windows PowerShell][powershell-here-strings].

5. Чтобы убедиться в том, что только строки, столбец t4 которых содержит значение **[ERROR]**, сохранены в таблице **errorLogs**, воспользуйтесь следующим запросом, чтобы отобразить все строки из таблицы **errorLogs**.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	В результате операции должны быть возвращены три строки, у которых столбец t4 содержит значение **[ERROR]**.


> [AZURE.NOTE] Если это необходимо, вы можете отправить результат вашего запроса в Microsoft Excel для последующего анализа. Дополнительную информацию см. в разделе [Соединение Excel с Hadoop с помощью Power Query][import-to-excel].

##<a id="runhivefromvs"></a>Выполнение запросов Hive с помощью Visual Studio
Инструменты HDInsight для Visual Studio поставляются с пакетом SDK для Azure для .NET версии 2.5 или более поздней.  Используя инструменты Visual Studio, вы можете подключить кластер HDInsight, создать таблицы Hive и выполнить Hive-запросы.  Дополнительную информацию см. в статье [Get started using HDInsight Hadoop Tools for Visual Studio][1] (Приступая к работе с инструментами HDInsight Hadoop Tools для Visual Studio).



##<a id="usetez"></a>Использование Tez для лучшей производительности

[Apache Tez][apache-tez] - это платформа, которая позволяет приложениям, работающим с большими объемами данных, таким как Hive, работать с большей производительностью. В последней версии HDInsight, выполнение заданий на Tez поддерживается Hive.  В данный момент эта возможность не задействована по умолчанию, вам требуется ее включить.  В будущих версиях программы данная возможность будет задействована по умолчанию. Для того чтобы воспользоваться преимуществами работы с Tez, необходимо установить для запроса Hive следующие значения переменных:

		set hive.execution.engine=tez;

Данные характеристики можно настроить для определенных запросов, поместив их в начале кода вашего запроса.  Можно задействовать эти характеристики по умолчанию для кластера, если установить значения переменных в настройках одинаковыми со временем создания кластера.  За более подробной информацией обращайтесь к разделу [Распределение кластеров HDInsight][hdinsight-provision].

Раздел [Документация по работе Hive на Tez][hive-on-tez-wiki] содержит большое количество полезной информации по реализации этого решения и вариантам настроек.


##<a id="nextsteps"></a>Дальнейшие действия

Hive упрощает запрос данных с помощью SQL-подобного языка запросов, но другие доступные в HDInsight компоненты предоставляют дополнительные функциональные возможности, такие как перемещение и преобразование данных. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с инструментами HDInsight Hadoop для Visual Studio][1].
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Использование Pig с HDInsight](../hdinsight-use-pig/)
* [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-data]
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Приступая к работе с Azure HDInsight.](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/ru-ru/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=35.1-->
