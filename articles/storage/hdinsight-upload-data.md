<properties 
	pageTitle="Отправка данных для заданий Hadoop в HDInsight | Microsoft Azure" 
	description="Вы узнаете, как отправить и получить доступ к данным в HDInsight с помощью обозревателя хранилищ Azure, Azure PowerShell, командной строки Hadoop или Sqoop." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#Отправка данных для заданий Hadoop в HDInsight

Служба Azure HDInsight — это полнофункциональная распределенная файловая система Hadoop (HDFS), в основе которой лежит хранилище BLOB-объектов Azure. Разработанная как дополнение для HDFS, она обеспечивает клиентам высочайшее удобство работы. Все компоненты экосистемы Hadoop могут непосредственно работать с данными, которыми управляет служба. Хранилище BLOB-объектов Azure и HDFS — это разные файловые системы, оптимизированные для хранения и обработки данных. Сведения о преимуществах хранилища BLOB-объектов Azure см.в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

Кластеры Azure HDInsight обычно развертываются для выполнения заданий MapReduce, а после завершения этих заданий удаляются. Хранение данных в кластерах HDFS после выполнения расчетов было бы дорогостоящим способом хранения информации. Хранилище BLOB-объектов Azure — это доступное, высокомасштабируемое, экономичное и общедоступное решение для хранения данных, которые должны обрабатываться с помощью HDInsight. Хранение информации в виде больших двоичных объектов дает возможность удалять кластеры HDInsight после завершения расчетов без угрозы потери данных.

Доступ к хранилищу BLOB-объектов Azure может осуществляться через [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [клиентскую библиотеку хранилища Azure для .NET][azure-storage-client-library], [интерфейс командной строки Azure для Mac, Linux и Windows][xplatcli] или с помощью средств проводника. Вот некоторые из доступных средств.

* [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/);
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/);
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer);
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx);
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx).

##Предварительные требования

Перед началом работы необходимо иметь следующее:

* Кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].


##<a id="azcopy"></a>Отправка данных в хранилище BLOB-объектов Azure с помощью AzCopy##

AzCopy — это программа командной строки, которая упрощает перенос данных в учетную запись хранения Azure и из нее. Ее можно использовать отдельно или добавить в существующее приложение. [Загрузка AzCopy][azure-azcopy-download].

Синтаксис AzCopy:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Дополнительные сведения см. в статье [AzCopy — отправка и скачивание файлов для BLOB-объектов Azure][azure-azcopy].

##<a id="powershell"></a>Отправка данных в хранилище BLOB-объектов Azure с помощью Azure PowerShell##

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Azure PowerShell можно использовать для отправки данных в хранилище BLOB-объектов Azure. Таким образом задания MapReduce смогут обрабатывать эти данные. Сведения о настройке Azure PowerShell на рабочей станции см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

**Отправка локального файла в хранилище BLOB-объектов Azure**

1. Откройте консоль Azure PowerShell, как описано в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
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
		
3. Вставьте сценарий в окно консоли Azure PowerShell, чтобы выполнить его.

В контейнерах хранилища BLOB-объектов данные хранятся в виде пар «ключ — значение». Иерархия каталогов отсутствует. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. Например, ключ BLOB-объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога "input" не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу. В предыдущем сценарии вы можете задать для файла структуру папок, задав переменную **$blobname**, например *$blobname="myfolder/myfile.txt"*.

Если вы пользуетесь одним из приложений Azure Explorer, вы, возможно, обратите внимание, что некоторые файлы имеют размер 0 байт. Эти файлы служат двум целям.

- Если есть пустые папки, эти файлы обозначают существование таких папок. Хранилище BLOB-объектов понимает, что если существует BLOB-объект с названием foo/bar, то существует и папка с именем **foo**. Но единственный способ обозначить пустую папку **foo** — это хранить в ней специальный файл размером 0 байт.
- Эти файлы содержат специальные метаданные, которые нужны файловой системе Hadoop, в частности сведения о разрешениях и имена владельцев папок.


##<a id="xplatcli"></a>Отправка данных в хранилище BLOB-объектов Azure с помощью интерфейса командной строки Azure

Интерфейс командной строки Azure для Mac, Linux и Windows представляет собой кроссплатформенное средство, с помощью которого можно управлять службами Azure. Чтобы отправить данные в хранилище BLOB-объектов Azure, выполните следующие действия.

1. [Установите и настройте интерфейс командной строки Azure для Mac, Linux и Windows](../xplat-cli.md).

2. Откройте командную строку, например bash или другую оболочку, и войдите в свою учетную запись с подпиской Azure с помощью следующей команды.

		azure login

	При появлении запроса введите имя пользователя и пароль для своей подписки.

3. Используйте следующую команду для отображения учетных записей хранения, связанных с вашей подпиской.

		azure storage account list

4. Выберите учетную запись хранения, которая содержит нужный для работы BLOB-объект, а затем с помощью следующей команды получите ключ для этой учетной записи.

		azure storage account keys list <storage-account-name>

	Команда должна вернуть **первичный** и **вторичный** ключи. Скопируйте значение **первичного** ключа, так как оно понадобится позже.

5. С помощью следующей команды получите список контейнеров BLOB-объектов в учетной записи хранения.

		azure storage container list -a <storage-account-name> -k <primary-key>

6. С помощью следующих команд отправьте и скачайте файлы в BLOB-объект.

	* Для отправки файла:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Для скачивания файла:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Если вы всегда будете работать с одной и той же учетной записью хранения, не указывайте учетную запись и ключ для каждой команды. Вместо этого задайте следующие переменные среды.
> 
> * **AZURE_STORAGE_ACCOUNT** — имя учетной записи хранения.
> 
> * **AZURE_STORAGE_ACCESS_KEY** — ключ учетной записи хранения.

##<a id="storageexplorer"></a>Отправка данных в хранилище BLOB-объектов с помощью Azure Storage Explorer

*Azure Storage Explorer* — это полезное средство для проверки и изменения данных в хранилище Azure. Программу можно бесплатно скачать с сайта CodePlex: [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/ "Обозреватель хранилищ Azure").

Прежде чем использовать средство, необходимо узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Инструкции по получению этой информации см. в статье [Об учетных записях хранения Azure][azure-create-storage-account] в разделе «Просмотр, копирование и повторное создание ключей доступа к хранилищу».

1. Запустите обозреватель хранилищ Azure.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Щелкните **Добавить учетную запись**. Добавив один раз учетную запись в обозреватель хранилищ Azure, выполнять этот шаг больше не требуется.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Заполните поля **Имя учетной записи хранения** и **Ключ учетной записи хранения**, а затем щелкните **Добавить учетную запись хранения**. Можно добавить несколько учетных записей хранения, и каждая из них будет отображаться на вкладке.
4. В разделе **Тип хранилища** щелкните **BLOB-объекты**.

	![HDI.ASEBlob][image-ase-blob]

5. В разделе **Контейнер** щелкните имя контейнера, который связан с вашим кластером HDInsight. Контейнер необходимо указать при создании кластера HDInsight. В противном случае процесс создания кластера создает его автоматически.
6. В разделе **BLOB-объект** щелкните **Отправить**.
7. Укажите файл для отправки и щелкните **Открыть**.

##<a id="commandline"></a> Отправка данных в хранилище BLOB-объектов с помощью командной строки Hadoop

Чтобы использовать командную строку Hadoop, сначала необходимо подключиться к кластеру с помощью удаленного рабочего стола.



1. Войдите на [портал Azure][azure-management-portal].
2. Щелкните **HDINSIGHT**. Вы увидите список развернутых кластеров Hadoop.
3. Щелкните кластер HDInsight, в который хотите отправить данные.
4. В верхней части страницы щелкните **КОНФИГУРАЦИЯ**.
5. Если удаленный рабочий стол не включен, щелкните **ВКЛЮЧИТЬ УДАЛЕННЫЙ ДОСТУП** и следуйте инструкциям. В противном случае перейдите к следующему шагу.
4. В нижней части страницы щелкните **ПОДКЛЮЧИТЬСЯ**.
7. Щелкните **Открыть**.
8. Введите свои учетные данные и нажмите кнопку **ОК**.
9. Щелкните **Да**.
10. На рабочем столе щелкните **Командная строка Hadoop**.
12. В приведенном ниже примере кода показано, как скопировать файл davinci.txt из локальной файловой системы на головном узле HDInsight в каталог /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Поскольку по умолчанию используется файловая система хранилища BLOB-объектов Azure, файл /example/datadavinci.txt фактически находится в хранилище BLOB-объектов Azure. Можно также использовать следующую ссылку на файл:

		wasb:///example/data/davinci.txt 

	или

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	При использовании *wasbs* полное доменное имя является обязательным.

13. Чтобы просмотреть список отправленных файлов, используйте следующую команду:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Импорт данных в HDFS из базы данных SQL Azure или сервера SQL Server с помощью Sqoop

Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД. Дополнительные сведения см. в разделе [Руководство пользователя Sqoop][apache-sqoop-guide].

Прежде чем импортировать данные, необходимо узнать имя сервера, на котором размещена база данных SQL Azure, имя учетной записи базы данных, пароль учетной записи и имя базы данных.

По умолчанию в базе данных SQL Azure разрешены подключения из служб Azure, в частности из службы Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале Azure. Инструкции по созданию базы данных SQL Azure и настройке правил брандмауэра см. в статье [Создание и настройка базы данных SQL Azure][sqldatabase-create-configure].

В приведенной ниже процедуре для отправки задания Sqoop используется Azure PowerShell.

**Импорт данных в HDInsight с помощью Sqoop и Azure PowerShell**

1. Откройте консоль Azure PowerShell, как описано в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
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

3. Вставьте сценарий в окно консоли Azure PowerShell, чтобы выполнить его. Пример извлечения файла данных из хранилища BLOB-объектов Azure см. в статье [Приступая к работе с HDInsight][hdinsight-get-started].

Дополнительные сведения об использовании Sqoop см. в статье [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop].

## Дальнейшие действия
Теперь, когда вы знаете, как передавать данные в HDInsight, узнайте, как их можно анализировать.

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight/hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight/hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight/hdinsight-use-pig.md
[hdinsight-provision]: hdinsight/hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[xplatcli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54--> 