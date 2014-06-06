<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Использование Hive с HDInsight" pageTitle="Использование Hive с HDInsight | Azure" metaKeywords="" description="Узнайте, как использовать Hive с HDInsight. Файл журнала будет использоваться в качестве входных данных в таблице HDInsight, а HiveQL будет использоваться для запроса данных и отчета по базовой статистике." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование Hive с HDInsight" authors=""  solutions="" writer="jgao" manager="paulettm" editor="mollybos"  />





# Использование Hive с HDInsight #

[Apache Hive][apache-hive] предоставляет средства для выполнения задания MapReduce через SQL-подобный язык сценариев под названием *HiveQL*, который может применяться к сведению данных, созданию запросов и анализу больших объемов данных. В этой статье вы будете использовать HiveQL для запроса данных в файле журнала log4j Apache и создания отчета по базовой статистике. 

**Предварительные требования:**

- Подготовленный **кластер HDInsight**. Пошаговое руководство по выполнению этой задачи с помощью портала Azure см. в разделе [Приступая к работе с HDInsight][hdinsight-getting-started]. Инструкции по другим способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. 

- Установленная среда **Azure PowerShell** на рабочей станции. Инструкции по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

**Предполагаемое время выполнения:** 30 минут



##Содержание

* [Пример использования Hive](#usage)
* [Передача файлов данных в хранилище BLOB-объектов Azure](#uploaddata)
* [Выполнение запросов Hive с помощью PowerShell](#runhivequeries)
* [Дальнейшие действия](#nextsteps)

##<a id="usage"></a>Пример использования Hive




Базы данных подходят для управления небольшими наборами данных, для которых допустимы запросы с небольшой задержкой. Однако когда речь идет о наборах данных большого размера, содержащих терабайты данных, традиционные базы данных SQL зачастую не являются идеальным решением. Администраторам баз данных для работы с такими большими наборами данных, как правило, приходится осуществлять масштабирование, приобретая более мощное оборудование, поскольку нагрузка на базу данных увеличивается, а это ведет к снижению производительности.  

Hive решает проблемы, связанные с большими объемами данных, позволяя пользователям осуществлять масштабирование при запросе больших наборов данных. Hive запрашивает данные параллельно на разных узлах с помощью технологии MapReduce, распределяя базу данных по большему числу узлов по мере увеличения нагрузки.

Hive и HiveQL предлагают также альтернативу созданию заданий MapReduce в Java при запросе данных. Предоставляется простая оболочка типа SQL, позволяющая записывать в HiveQL запросы, которые затем компилируются в MapReduce службой HDInsight и выполняются в кластере.
 
Hive также позволяет программистам, знакомым с платформой MapReduce, подключать пользовательские программы map и reduce для выполнения более сложного анализа, которой может не поддерживаться встроенными возможностями языка HiveQL.  

Hive лучше всего подходит для пакетной обработки больших объемов неизменяемых данных (например, веб-журналов). Но не подходит для приложений обработки транзакций, которым требуется очень маленькое время отклика, например систем управления базами данных. Система Hive оптимизирована для масштабируемости (можно динамически добавлять компьютеры в кластере Hadoop), расширяемости (в структуре MapReduce и с другими интерфейсами программирования) и отказоустойчивости. Задержка не является ключевым вопросом разработки.   

Как правило, приложения сохраняют ошибки, исключения и другие проблемы с кодом в файл журнала, поэтому администраторы могут использовать данные файлов журналов для изучения возникающих проблем или создавать показатели, имеющие отношение к ошибкам или другим проблемам (например, с производительностью). Эти файлы журналов обычно получаются довольно большими и содержат огромные массивы данных, которые необходимо обработать и исследовать в приложении. 

Таким образом, файлы журналов являются образцовым примером данных большого размера. HDInsight предоставляет систему хранилища данных Hive, которая обеспечивает удобное сведение данных, динамические запросы и анализ этих больших наборов данных, хранящихся в совместимых с Hadoop файловых системах, например в хранилище BLOB-объектов Azure.
















##<a id="uploaddata"></a>Отправка файлов данных в хранилище BLOB-объектов

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию.  Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

В этой статье вы используете образец файла log4j, который предоставляется с кластером HDInsight и хранится в папке *\example\data\sample.log*. Каждый журнал в файле состоит из строки полей, содержащей поле `[УРОВЕНЬ ЖУРНАЛА]` для отображения типа и серьезности. Например:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 


Чтобы получить доступ к файлам, используйте следующий синтаксис: 

	wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Например:

	wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Замените *mycontainer* именем контейнера, а *mystorage* — именем учетной записи хранилища BLOB-объектов. 

Поскольку файл хранится в файловой системе по умолчанию, можно также получить доступ к файлу, используя следующее:

	wasb:///example/data/sample.log
	/example/data/sample.log

Для создания собственных файлов log4j используйте средство ведения журналов [Apache Log4j][apache-log4j]. Сведения о передаче данных в хранилище BLOB-объектов Azure см. в разделе [Передача данных в HDInsight][hdinsight-upload-data].





















##<a id="runhivequeries"></a> Выполнение запросов Hive с помощью PowerShell
В последнем разделе вы отправили файл log4j с именем sample.log в контейнер файловой системы по умолчанию.  В этом разделе вы с помощью HiveQL создадите таблицу hive, загрузите данные в таблицу hive и запросите данные, чтобы узнать количество журналов ошибок.

В этой статье приводятся инструкции по использованию командлетов Azure PowerShell для выполнения запроса Hive. Перед изучением этого раздела необходимо настроить локальную среду, а также настроить подключение к Azure, как описано в разделе **Предварительные требования** в начале этой статьи.

Запросы Hive можно выполнять в PowerShell с помощью командлета **Start-AzureHDInsightJob** или **Invoke-Hive**.

**Выполнение запросов Hive с помощью Start-AzureHDInsightJob**

1. Откройте окно консоли PowerShell для Azure. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

2. Задайте переменные в следующем сценарии, затем запустите его:

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"
		
		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Выполните следующий сценарий, чтобы определить запросы HiveQL:

		# HiveQL queries
		# Use the internal table option. 
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
		               "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		# Use the external table option. 
		$queryString = "DROP TABLE log4jLogs;" +
		                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
				        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	Результатом команды HiveQL LOAD DATA является перемещение файла данных в папку \hive\warehouse\.  Команда DROP TABLE удаляет таблицу и файл данных.  Если используется внутренняя таблица, то перед повторным запуском сценария необходимо еще раз отправить файл sample.log. Если нужно сохранить файл данных, необходимо использовать команду CREATE EXTERNAL TABLE, как показано в сценарии.
	
	В ситуации, когда файл данных находится в другом контейнере или другой учетной записи хранения, можно также использовать внешнюю таблицу.

	Если при повторном запуске сценария таблица log4jlogs уже существует, то сначала используйте команду DROP TABLE.

4. Выполните следующий сценарий, чтобы создать определение задания Hive:
		
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
8. 
		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	Результаты:

		[ERROR] 3

**Отправка запросов Hive с помощью Invoke-Hive**

1. Откройте окно консоли PowerShell для Azure.
2. Выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.
2. Задайте переменную, а затем выполните:

		$clusterName = "<HDInsightClusterName>"

3. Выполните следующий сценарий, чтобы вызвать запросы HiveQL:

		Use-AzureHDInsightCluster $clusterName 
		$response = Invoke-Hive -Query @"
		    SELECT * FROM hivesampletable
		        WHERE devicemake LIKE "HTC%"
		        LIMIT 10; 
		"@
		
		Write-Host $response

	Результаты:

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	Для более длинных запросов HiveQL рекомендуется использовать конструкцию PowerShell Here-Strings или файл сценария HiveQL. В приведенных ниже примерах показано, как использовать командлет Invoke-Hive для запуска файла сценария HiveQL.  Файл сценария HiveQL необходимо передать в WASB.

		Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

	Дополнительные сведения о конструкции Here-Strings см. в разделе [Использование конструкции Here-Strings в Windows PowerShell][powershell-here-strings].
	
##<a id="nextsteps"></a>Дальнейшие действия

Hive упрощает запрос данных с помощью SQL-подобного языка запросов, но другие доступные в HDInsight компоненты предоставляют дополнительные функциональные возможности, такие как перемещение и преобразование данных. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](/ru-ru/manage/services/hdinsight/get-started-hdinsight/)
* [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-delay-data]
* [Использование Oozie с HDInsight][hdinsight-oozie]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование Pig с HDInsight](/ru-ru/manage/services/hdinsight/using-pig-with-hdinsight/)
* [Документация по пакету Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx
[azure-purchase-options]: https://www.windowsazure.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ru-ru/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ru-ru/pricing/free-trial/


[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j


[hdinsight-oozie]: /ru-ru/documentation/articles/hdinsight-use-oozie/
[hdinsight-analyze-flight-delay-data]: /ru-ru/documentation/articles/hdinsight-analyze-flight-delay-data/


[hdinsight-storage]: /ru-ru/manage/services/hdinsight/howto-blob-store

[hdinsight-provision]: /ru-ru/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-submit-jobs]: /ru-ru/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: /ru-ru/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-configure-powershell]: /ru-ru/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-getting-started]: /ru-ru/manage/services/hdinsight/get-started-hdinsight/

[Powershell-install-configure]: /ru-ru/documentation/articles/install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/ru-ru/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png 
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png 


