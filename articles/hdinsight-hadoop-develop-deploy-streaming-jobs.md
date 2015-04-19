<properties 
	pageTitle="Разработка программ потоковой передачи Hadoop на C# для HDInsight на платформе Azure" 
	description="Вы узнаете, как разработать программы потоковой передачи MapReduce на C#, а также о том, как загрузить их на платформу Azure HDInsight." 
	services="hdinsight" 
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
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# Разработка программ потоковой передачи Hadoop на C# для HDInsight

Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. Этот учебник поможет вам подробно разобраться с процессом подсчета слов и узнать частоту употребления заданного слова во введенных данных. На следующем изображении вы можете увидеть, как платформа MapReduce осуществляет подсчет слов.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

В этом разделе вы узнаете, как:

- разработать и провести тестирование программы MapReduce потоковой передачи Hadoop на C# с помощью эмулятора HDInsight;
- выполнить то же задание MapReduce в Azure HDInsight; 
- получить результаты задания MapReduce.

**Предварительные требования:**

Перед началом работы с этим учебником необходимо выполнить следующие действия:

- Установите эмулятор Azure HDInsight. Указания см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-get-started-emulator].
- Установите среду Azure PowerShell на компьютере с эмулятором. Указания см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
- Получите подписку Azure. Указания см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатная пробная версия][azure-free-trial].

##Содержание

- [Разработка на C# программы потоковой передачи Hadoop для счетчика слов](#develop)
- [Тестирование программы в эмуляторе](#test)
- [Передача файлов данных и приложения в хранилище BLOB-объектов Azure](#upload)
- [Выполнение программы MapReduce в Azure HDInsight](#run)
- [Извлечение результатов MapReduce](#retrieve)
- [Дальнейшие действия](#nextsteps)

##<a name="develop"></a>Разработка на C&#35; программы потоковой передачи Hadoop для счетчика слов

Решение для подсчета слов содержит два проекта консольного приложения: mapper и reducer. Приложение mapper передает каждое слово на консоль, а приложение reducer подсчитывает количество слов, переданных из документа. Модуль сопоставления и модуль редукции считывают символы (построчно) из стандартного входного потока (stdin) и записывают данные в стандартный выходной поток (stdout).

**Создание консольного приложения на C#**

1. Откройте Visual Studio 2013.
2. Выберите **ФАЙЛ**, **Создать** и **Проект**.
3. Введите или выберите следующие значения:

	<table border="1">
	<tr><td>Поле</td><td>Значение</td></tr>
	<tr><td>Шаблон</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Имя</td><td>WordCountMapper</td></tr>
	<tr><td>Расположение</td><td>C:\Tutorials</td></tr>
	<tr><td>Имя решения</td><td>WordCount</td></tr>
	</table>
	
4. Нажмите кнопку **ОК**, чтобы создать проект.

**Создание программы Mapper**

5. В обозревателе решений щелкните правой кнопкой мыши **Program.cs**, затем выберите пункт **Переименовать**.
6. Измените имя файла на **WordCountMapper.cs**, а затем нажмите клавишу **ВВОД**.
7. Щелкните **Да**, чтобы подтвердить переименование всех ссылок.
8. Дважды щелкните **WordCountMapper.cs**, чтобы открыть файл.
9. Добавьте следующую инструкцию using:

		using System.IO;

10. Замените функцию Main() следующим кодом:

		static void Main(string[] args)
		{
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    string line;
		    string[] words;
		
		    while ((line = Console.ReadLine()) != null)
		    {
		        words = line.Split(' ');
		
		        foreach (string word in words)
		            Console.WriteLine(word.ToLower());
		    }
		}

11. Щелкните **СБОРКА**, а затем щелкните **Построить решение** для компиляции программы Mapper.	


**Создание программы Reducer**

1. В Visual Studio 2013 выберите **ФАЙЛ**, **Добавить**, **Новый проект**.
2. Введите или выберите следующие значения:

	<table border="1">
	<tr><td>Поле</td><td>Значение</td></tr>
	<tr><td>Шаблон</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Имя</td><td>WordCountReducer</td></tr>
	<tr><td>Расположение</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3. Снимите флажок **Создать каталог решения**, затем нажмите кнопку **ОК**, чтобы создать проект.
4. В обозревателе решений щелкните правой кнопкой мыши **Program.cs**, затем выберите пункт **Переименовать**.
5. Измените имя файла на **WordCountReducer.cs**, а затем нажмите клавишу **ВВОД**.
7. Щелкните **Да**, чтобы подтвердить переименование всех ссылок.
8. Дважды щелкните **WordCountReducer.cs**, чтобы открыть файл.
9. Добавьте следующую инструкцию using:

		using System.IO;

10. Замените функцию Main() следующим кодом:

		static void Main(string[] args)
		{
		    string word, lastWord = null;
		    int count = 0;
		
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    while ((word = Console.ReadLine()) != null)
		    {
		        if (word != lastWord)
		        {
		            if(lastWord != null)
		                Console.WriteLine("{0}[{1}]", lastWord, count);
		
		            count = 1;
		            lastWord = word;
		        }
		        else
		        {
		            count += 1; 
		        }
		    }
		    Console.WriteLine(count);
		}

11. Щелкните **СБОРКА**, а затем щелкните **Построить решение** для компиляции программы Reducer.	

Исполняемые файлы программ Mapper и Reducer находятся в следующих папках:

- C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe
- C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe


##<a name="test"></a>Тестирование программы в эмуляторе

Для тестирования программы в эмуляторе выполните следующие действия:

1. Отправьте данные в эмулятор HDFS.
2. Отправьте программы Mapper и Reducer в эмулятор HDFS.
3. Запустите задание MapReduce для подсчета слов.
4. Проверьте состояние задания.
5. Извлеките результаты задания.

В эмуляторе HDInsight используется HDFS в качестве файловой системы по умолчанию.  При желании вы можете настроить эмулятор HDInsight на использование хранилища BLOB-объектов Azure. Дополнительную информацию см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator-wasb]. В этом разделе для передачи файлов будет использоваться команда HDFS *copyFromLocal*. В следующем разделе будет показано, как передать файлы с помощью Azure PowerShell. Другие способы см. в разделе [Передача данных в HDInsight][hdinsight-upload-data].

Для работы с этим учебником используется следующая структура папок:

<table border="1">
<tr><td>Папка</td><td>Примечание.</td></tr>
<tr><td>\WordCount</td><td>Корневая папка для проекта счетчика слов. </td></tr>
<tr><td>\WordCount\Apps</td><td>Папка исполняемых файлов программ mapper и reducer.</td></tr>
<tr><td>\WordCount\Input</td><td>Папка исходных файлов MapReduce.</td></tr>
<tr><td>\WordCount\Output</td><td>Папка выходных файлов MapReduce.</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>Выходная папка заданий.</td></tr>
</table></br>

В этом учебнике используются TXT-файлы, расположенные в каталоге %hadoop_home%.

> [AZURE.NOTE] В командах Hadoop HDFS учитывается регистр.

**Копирование текстовых файлов в HDFS эмулятора**

1. В окне командной строки Hadoop выполните следующую команду, чтобы создать каталог для входных файлов:

		hadoop fs -mkdir /WordCount/
		hadoop fs -mkdir /WordCount/Input

	Здесь используется относительный путь. Эта запись аналогична следующей:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Выполните следующую команду, чтобы скопировать некоторые текстовые файлы во входную папку в HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common\*.txt \WordCount\Input

3. Выполните следующую команду, чтобы получить список переданных файлов:

		hadoop fs -ls \WordCount\Input

	


**Развертывание программ Mapper и Reducer в HDFS эмулятора**

1. Откройте командную строку Hadoop на вашем рабочем столе и создайте папку /Apps для программы в HDFS
		hadoop fs -mkdir /WordCount/Apps

2. Выполните следующие команды:

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. Выполните следующую команду, чтобы получить список переданных файлов:

		hadoop fs -ls /WordCount/Apps

	Должны появиться два EXE-файла.


**Запуск задания MapReduce с помощью HDInsight PowerShell**

1. Откройте Azure PowerShell. Указания см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. 
3. Выполните следующие команды, чтобы задать переменные:

		$clusterName = "http://localhost:50111"
		
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	Имя кластера эмулятора HDInsight: "http://localhost:50111".  

4. Выполните следующие команды, чтобы определить задание потоковой передачи:

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. Выполните следующую команду, чтобы создать объект учетных данных:

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	Появится запрос на ввод пароля.  Паролем может быть любая строка.  Используйте имя пользователя "hadoop".

6. Выполните следующие команды, чтобы отправить задание MapReduce и ждать завершения задания:
		
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

	После завершения вы получите результат следующего вида:

		StatusDirectory : /WordCount/MRStatusOutput
		ExitCode        : 
		Name            : mrWordCountStreamingJob
		Query           : 
		State           : Completed
		SubmissionTime  : 11/15/2013 7:18:16 PM
		Cluster         : http://localhost:50111
		PercentComplete : map 100%  reduce 100%
		JobId           : job_201311132317_0034
		
	Вы можете увидеть идентификатор задания в выходных данных, например *job-201311132317-0034*. 

**Проверка состояния задания**

1. На рабочем столе щелкните **Состояние Hadoop YARN** или перейдите к файлу **http://localhost:50030/jobtracker.jsp**.
2. Найдите задание с помощью идентификатора в категории **ЗАПУЩЕННЫЕ** или **ЗАВЕРШЕННЫЕ**. 
3. Если задание не выполнено вследствие ошибки, вы можете найти его в категории **НЕВЫПОЛНЕННЫЕ**. В этом случае вы можете открыть сведения о задании и найти там полезную информацию для отладки.


**Отображение выходных данных из HDFS**

1. Откройте командную строку Hadoop.
2. Выполните следующие команды, чтобы отобразить выходные данные:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	Чтобы получить страничное представление, добавьте в конце команды "|more".

##<a id="upload"></a>Отправка данных в хранилище BLOB-объектов Azure
Служба Azure HDInsight использует хранилище BLOB-объектов Azure в качестве файловой системы по умолчанию. Можно настроить кластер HDInsight на использование дополнительного хранилища BLOB-объектов для хранения файлов данных. В этом разделе вы создадите учетную запись хранения и передадите файлы данных в хранилище BLOB-объектов. В качестве файлов данных используются TXT-файлы в каталоге %hadoop_home%\share\doc\hadoop\common.


**Создание хранилища BLOB-объектов и контейнера**

1. Откройте Azure PowerShell.
2. Задайте переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Выполните следующие команды, чтобы создать учетную запись хранения и контейнер хранилища BLOB-объектов в учетной записи

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Выполните следующие команды, чтобы проверить учетную запись хранения и контейнер:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Передача файлов данных**

1. Внесите данные об исходной и целевой папках в окне Azure PowerShell.

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Заметьте, что исходной является папка **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common**, а целевой - папка **WordCount/Input**. Исходное расположение - это расположение TXT-файла в эмуляторе HDInsight. Целевое расположение - это структура папок, которая будет отображаться в контейнере BLOB-объектов Azure.

3. Чтобы получить список TXT-файлов в исходной папке, выполните следующие команды:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
5. Чтобы скопировать файлы, выполните следующий фрагмент кода:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Выполните следующую команду, чтобы получить список переданных файлов:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**Передача приложений счетчика слов**

1. Установите следующие переменные в окне Azure PowerShell:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Обратите внимание на то, что целевой папкой является **WordCount/Apps**. Эта же структура папок будет отображаться в контейнере BLOB-объектов Azure.

2. Выполните следующие команды, чтобы скопировать приложения:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Выполните следующую команду, чтобы получить список переданных файлов:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Должны появиться оба указанных файла программы.


##<a name="run"></a>Выполнение задания MapReduce в Azure HDInsight

В этом разделе разбирается сценарий PowerShell для выполнения всех возможных задач, связанных с выполнением задания MapReduce. В список заданий входят:

1. Подготовка кластера HDInsight
	
	1. Создание учетной записи хранения, которая будет использоваться в качестве файловой системы по умолчанию для кластера HDInsight
	2. Создание контейнера хранилища BLOB-объектов 
	3. Создание кластера HDInsight

2. Отправка задания MapReduce

	1. Создание определения задания MapReduce для потоковой передачи
	2. Отправка задания MapReduce
	3. Ожидание завершения задания
	4. Отображение стандартной ошибки
	5. Отображение стандартного вывода

3. Удаление кластера

	1. Удаление кластера HDInsight
	2. Удаление учетной записи хранения, которая использовалась в качестве файловой системы по умолчанию для кластера HDInsight


**Запуск сценария PowerShell**

1. Откройте Блокнот.
2. Скопируйте следующий код и вставьте в Блокнот:
		
		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### prefix to cluster, storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = 1
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		Select-AzureSubscription $subscriptionName
		
		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config
		
		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
		
		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)
		
		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
		
		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName 
		
		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Задайте первые четыре переменных скрипта: **$stringPrefix** используется для добавления префикса к имени кластера HDInsight, имени учетной записи хранения и имени контейнера хранилища BLOB-объектов. Поскольку количество символов в названии должно быть больше 3 и меньше 24, следите за тем, чтобы общее количество символов в строке и именах, используемых сценарием, не превышало максимального значения. В параметре $stringPrefix используются символы только нижнего регистра.

	 **$storageAccountName_Data** и **$containerName_Data** это учетная запись хранилища и контейнер, которые вы создали ранее. поэтому им необходимо присвоить имена. Они предназначены для хранения файлов данных и приложений. Значение $location должно соответствовать расположению учетной записи хранения данных.

4. Просмотрите остальные переменные.
5. Сохраните файл скрипта.
6. Откройте Azure PowerShell.
7. Выполните следующую команду, чтобы задать политику выполнения remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. При появлении запроса введите имя пользователя и пароль для кластера HDInsight. Следите за тем, чтобы поле пароля содержало не менее 10 символов, среди которых должны быть прописная буква, строчная буква, число и специальный знак. Информацию о том, что необходимо сделать, чтобы запрос на ввод учетных данных не появлялся, см. в разделе [Работа с паролями, защищенными строками и учетными данными в Windows PowerShell][powershell-PSCredential]

Пример пакета SDK HDInsight .NET по отправке потоковых заданий Hadoop см. в разделе [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs].


##<a name="retrieve"></a>Извлечение выходных данных задания MapReduce
В этом разделе показано, как загружать и отображать выходные данные.  Дополнительную информацию об отображении результатов в Excel см. в разделах [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC] и [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query].


**Извлечение результатов**

1. Откройте окно Azure PowerShell.
2. Задайте значения, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"
	
3. Выполните следующие команды для создания объекта контекста хранилища Azure: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

4. Выполните следующие команды, чтобы загрузить и отобразить выходные данные:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

	

##<a id="nextsteps"></a>Дальнейшие действия
В этом учебнике вы узнали, как разработать потоковое задание Hadoop в MapReduce, протестировать приложение в эмуляторе HDInsight, написать скрипт PowerShell для подготовки кластера HDInsight и выполнения MapReduce в кластере. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с Azure HDInsight](hdinsight-get-started.md)
- [Приступая к работе с эмулятором HDInsight][hdinsight-get-started-emulator]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]
- [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator/#blobstorage
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "MapReduce wordcount application flow"






<!--HONumber=42-->
