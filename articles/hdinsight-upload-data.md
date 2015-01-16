<properties urlDisplayName="Upload Data" pageTitle="Отправка данных для заданий Hadoop в Azure HDInsight" metaKeywords="" description="Вы узнаете, как отправить и получить доступ к данным в HDInsight с помощью обозревателя хранилищ Azure, Azure PowerShell, командной строки Hadoop или Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data for Hadoop jobs in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />



#Отправка данных для заданий Hadoop в HDInsight

Azure HDInsight предоставляет полнофункциональную распределенную файловую систему Hadoop (HDFS) через хранилище BLOB-объектов Azure. Это решение создано как расширение HDFS, которое обеспечивает удобную работу, подключая полный набор компонентов экосистемы Hadoop, применяемых непосредственно к обрабатываемым данным. Хранилище больших двоичных объектов Azure и HDFS - это разные файловые системы, оптимизированные соответственно для хранения данных и для выполнения расчетов с этими данными. Преимущества использования хранилища больших двоичных объектов Azure см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

Кластеры Azure HDInsight обычно развертываются для выполнения заданий MapReduce и удаляются сразу после завершения этих заданий. Хранение данных в кластерах HDFS после выполнения расчетов было бы дорогостоящим способом хранения данных. Хранилище больших двоичных объектов Azure - это доступное, высокомасштабируемое, экономичное и общедоступное решение для хранения данных, которые должны обрабатываться с помощью HDInsight. Хранение данных в BLOB-объектах позволяет освобождать кластеры HDInsight, используемые для расчетов, без потери данных. 

Доступ к хранилищу больших двоичных объектов Azure может осуществляться через [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [клиентскую библиотеку хранилища Azure для .NET][azure-storage-client-library] или с помощью средств проводника. Некоторые из доступных средств:

* [Обозреватель хранилищ Azure](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

* Кластер Azure HDInsight. Указания см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].

##Содержание

* [Отправка данных в хранилище больших двоичных объектов с использованием AzCopy](#azcopy)
* [Отправка данных в хранилище больших двоичных объектов с использованием Azure PowerShell](#powershell)
* [Отправка данных в хранилище больших двоичных объектов с использованием обозревателя хранилищ Azure](#storageexplorer)
* [Отправка данных в хранилище больших двоичных объектов с использованием командной строки Hadoop](#commandline)
* [Импорт данных из базы данных SQL Azure в хранилище больших двоичных объектов с использованием Sqoop](#sqoop)

##<a id="azcopy"></a>Отправка данных в хранилище больших двоичных объектов с использованием AzCopy

AzCopy - это служебная программа командной строки, разработанная для упрощения задачи переноса данных в учетную запись хранения Azure и из нее. Ее можно использовать как автономное средство или включить в существующее приложение. [Загрузка AzCopy][azure-azcopy-download].

Синтаксис AzCopy:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Дополнительную информацию см. в статье [AzCopy - Uploading/Downloading files for Azure Blobs][azure-azcopy] (AzCopy - отправка и скачивание файлов для больших двоичных объектов Azure).

##<a id="powershell"></a>Отправка данных в хранилище BLOB-объектов с использованием Azure PowerShell##

Azure PowerShell - это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. Azure PowerShell можно использовать для отправки данных в хранилище BLOB-объектов, чтобы данные могли обрабатываться заданиями MapReduce. Информация о настройке рабочей станции для запуска Microsoft Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

**Отправка локального файла в хранилище больших двоичных объектов**

1. Откройте окно консоли Azure PowerShell, как указано в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Задайте значения первых пяти переменных в следующем сценарии:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Вставьте скрипт в окно консоли Azure PowerShell, чтобы выполнить его.

В контейнерах хранилища BLOB-объектов данные хранятся в виде пар "ключ-значение" и отсутствует иерархия каталогов. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. Например, ключ большого двоичного объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога "input" не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу. В предыдущем сценарии вы можете связать с файлом структуру папок, задав переменную $blobname . Например: *$blobname="myfolder/myfile.txt"*.

С помощью средств проводника Azure вы можете заметить некоторые файлы размером 0 байт. Эти файлы служат двум целям.

- В случае пустых папок они обозначают существование папки. Хранилище BLOB-объектов понимает, что если существует BLOB-объект с названием foo/bar, то существует и папка с именем foo. Но если вы хотите иметь пустую папку с именем foo, то единственный способ обозначить ее - создать в нужном месте специальный файл размером 0 байт.
- Они содержат некоторые специальные метаданные, необходимые файловой системе Hadoop, в частности разрешения и имена владельцев для папок.








##<a id="storageexplorer"></a>Отправка данных в хранилище BLOB-объектов с использованием обозревателя хранилищ Azure

Обозреватель хранилищ Azure - это полезный инструмент для проверки и изменения данных в хранилище Azure. Это бесплатный инструмент, который можно скачать на сайте [http://azurestorageexplorer.codeplex.com/](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Прежде чем использовать средство, необходимо узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Указания по получению этой информации см. в "Практическом руководстве: Просмотр, копирование и повторное создание ключей доступа к хранилищу" раздела [Создание, управление и удаление учетной записи хранения][azure-create-storageaccount].  

1. Запустите обозреватель хранилищ Azure.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Щелкните **Добавить учетную запись**. Добавив один раз учетную запись в обозреватель хранилищ Azure, выполнять этот шаг больше не требуется. 

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Введите **Имя учетной записи хранения** и **Ключ учетной записи хранения**, затем щелкните **Добавить учетную запись хранения**. Можно добавить несколько учетных записей хранения, и каждая из них будет отображаться на вкладке. 
4. В разделе **Тип хранилища** щелкните **Большие двоичные объекты**.

	![HDI.ASEBlob][image-ase-blob]

5. В разделе **Контейнер** дважды щелкните контейнер, связанный с вашим кластером HDInsight. Контейнер необходимо указать при создании кластера HDInsight.  В противном случае процесс создания кластера создает его автоматически.
6. В разделе **Большой двоичный объект** щелкните **Отправить**.
7. Укажите файл для отправки и щелкните **Открыть**.








































































##<a id="commandline"></a> Отправка данных в хранилище BLOB-объектов с использованием командной строки Hadoop

Чтобы использовать командную строку Hadoop, необходимо сначала подключиться к кластеру с помощью удаленного рабочего стола. 



1. Войдите на [Портал управления][azure-management-portal].
2. Щелкните **HDINSIGHT**. Вы увидите список развернутых кластеров Hadoop.
3. Щелкните кластер HDInsight, в который хотите отправить данные.
4. В верхней части страницы щелкните **КОНФИГУРАЦИЯ**.
5. Если удаленный рабочий стол не включен, щелкните**ВКЛЮЧИТЬ УДАЛЕННЫЙ ДОСТУП** и следуйте указаниям.  В противном случае перейдите к следующему шагу.
4. В нижней части страницы щелкните **ПОДКЛЮЧИТЬСЯ**.
7. Щелкните **Открыть**.
8. Введите свои учетные данные и нажмите кнопку **ОК**.
9. Щелкните **Да**.
10. На рабочем столе щелкните **Командная строка Hadoop**.
12. В приведенном ниже примере показано, как скопировать файл davinci.txt из локальной файловой системы на головном узле HDInsight в каталог /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Так как по умолчанию используется файловая система хранилища BLOB-объектов Azure, файл фактически находится в хранилище BLOB-объектов Azure.  Можно также использовать следующую ссылку на файл:

		wasb:///example/data/davinci.txt 

	или

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	При использовании wasbs полное доменное имя является обязательным.

13. Чтобы просмотреть список отправленных файлов, используйте следующую команду:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Импорт данных в HDFS из базы данных SQL или SQL Server с использованием Sqoop

Sqoop - это инструмент, предназначенный для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД. Дополнительную информацию см. в разделе [Руководство пользователя Sqoop][apache-sqoop-guide].

Прежде чем импортировать данные, необходимо узнать имя сервера базы данных SQL Azure, имя учетной записи базы данных, пароль учетной записи и имя базы данных. 

По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Указания по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][sqldatabase-create-configue]. 

В следующей процедуре используется PowerShell для отправки задания Sqoop. 

**Импорт данных в HDInsight с помощью Sqoop и PowerShell**

1. Откройте окно консоли Azure PowerShell, как указано в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
2. Задайте значения первых восьми переменных в следующем сценарии:

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. Вставьте скрипт в окно консоли Azure PowerShell, чтобы выполнить его. Пример PowerShell для извлечения файла данных из хранилища больших двоичных объектов Azure см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started].

Дополнительную информацию о Sqoop см. в разделе [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

## Дальнейшие действия
Теперь, когда вы знаете, как получить данные в HDInsight, узнайте о том, как выполнять анализ, в следующих статьях:

* [Приступая к работе с Azure HDInsight.][hdinsight-get-started]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /ru-ru/develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-azcopy-download]: http://aka.ms/WaCopy
[azure-azcopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig
[hdinsight-provision]: ../hdinsight-provision-clusters/

[sqldatabase-create-configue]: ../sql-database-create-configure/

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../install-configure-powershell/


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=35.1-->
