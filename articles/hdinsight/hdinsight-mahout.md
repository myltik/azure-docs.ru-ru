<properties
	pageTitle="Создание рекомендаций с помощью HDInsight на основе Mahout и WIndows | Microsoft Azure"
	description="Узнайте, как использовать библиотеку машинного обучения Apache Mahout для создания списка рекомендуемых фильмов с помощью HDInsight (Hadoop) на платформе WIndows."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="larryfr"/>

#Создание списка рекомендуемых фильмов с использованием Apache Mahout и Hadoop в HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Узнайте, как использовать библиотеку машинного обучения [Apache Mahout](http://mahout.apache.org) для создания списка рекомендуемых к просмотру фильмов с помощью Azure HDInsight.

> [AZURE.NOTE]Действия, описанные в этом документе, требуют наличия клиента Windows и кластера HDInsight на основе Windows. Сведения об использовании Mahout из клиента Linux, OS X или Unix с кластером HDInsight под управлением Linux см. в разделе [Создание рекомендаций по фильмам с использованием Apache Mahout с Hadoop на основе Linux в HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>О чем вы узнаете

Mahout — это библиотека [машинного обучения][ml] для Apache Hadoop. Mahout содержит алгоритмы для обработки данных, такие как фильтрация, классификация и кластеризация. В этой статье вы будете использовать подсистему рекомендаций для создания рекомендаций к просмотру на основе фильмов, уже просмотренных вашими друзьями. Вы также узнаете о том, как выполнять классификацию с помощью леса деревьев принятия решений. Вы изучите следующее:

* Как запускать задания Mahout с помощью Windows PowerShell.

* как запускать задания Mahout из командной строки Hadoop;

* Как устанавливать Mahout на кластерах HDInsight версий 3.0 и 2.0.

	> [AZURE.NOTE]Mahout входит в состав кластеров HDInsight версии 3.1. Если вы используете более раннюю версию HDInsight, перед тем как продолжить, см. раздел [Установка Mahout](#install).

##Необходимые компоненты

* **Кластер Hadoop на платформе Windows в HDInsight**. Для получения информации о создании кластера см. раздел [Приступая к работе с Hadoop в HDInsight][getstarted].

- **Рабочая станция с Azure PowerShell**. См. раздел [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a name="recommendations"></a>Создание рекомендаций с использованием Windows PowerShell

> [AZURE.NOTE]В то время как задание, используемое в этом разделе, работает с Windows PowerShell, многие классы, предоставляемые Mahout, в настоящее время не работают с Windows PowerShell и должны запускаться из командной строки Hadoop. Список классов, которые не работают с Windows PowerShell, приведен в разделе [Устранение неполадок](#troubleshooting).
>
> Пример использования командной строки Hadoop для запуска заданий Mahout см. в разделе [Классификация данных с использованием командной строки Hadoop](#classify).

Одной из функций, предоставляемой Mahout, является подсистема рекомендаций. Она принимает данные в формате `userID`, `itemId` и `prefValue` (предпочтения пользователей для элемента). Затем Mahout может провести анализ совместной встречаемости, чтобы определить: _пользователи с предпочтениями к одному элементу также имеют их и к определенным другим элементам_. После этого Mahout определяет пользователей со сходными предпочтениями элементов, что можно использовать для создания рекомендаций.

Ниже приведен предельно простой пример с использованием фильмов:

* __Совместная встречаемость:__ Джо, Алисе и Бобу нравятся _Звездные войны_, _Империя наносит ответный удар_ и _Возвращение джедая_. Mahout определяет, что пользователям, которым нравится любой из этих фильмов, также нравятся другие два.

* __Совместная встречаемость:__ Бобу и Алисе также нравятся _Призрачная угроза_, _Атака клонов_ и _Месть ситхов_. Mahout определяет, что пользователям, которым нравится предыдущие три фильма, также нравятся и эти три.

* __Рекомендации на основе подобия:__ так как Джо понравились первые три фильма, Mahout будет отбирать фильмы, которые нравились другим пользователям со схожими предпочтениями, но которые Джо еще не смотрел (по положительным отзывам и рейтингу). В этом случае Mahout рекомендует посмотреть _Призрачную угрозу_, _Атаку клонов_ и _Месть ситхов_.

###Загрузка данных

Очень кстати, что компания [GroupLens Research][movielens] предоставляет данные о рейтинге фильмов в формате, совместимом с Mahout.

1. Скачайте архив [MovieLens 100k][100k], в котором содержатся 100 000 рейтинговых оценок от 1000 пользователей по 1700 фильмам.

2. Распакуйте архив. В не должен находиться каталог __ml-100k__, содержащий множество файлов с данными, имеющих префикс __u.__. Файл, который будет анализироваться Mahout, называется __u.data__. Структура данных этого файла: `movieID`, `userID`, `userRating` и `timestamp`. Вот пример данных:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. Передайте файл __u.data__ в каталог __example/data/u.data__ на кластере HDInsight. Следующая команда использует PowerShell для передачи данных. Другие способы загрузки файлов можно найти в документе [Отправка данных для заданий Hadoop в HDInsight][upload].

        # Put your cluster name below
        $clusterName="Your HDInsight cluster name"
        # Put the path to the u.data file below
        $fileToUpload="The path to the u.data file"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/data/u.data" `
            -Container $container `
            -Context $context
    
    Она передает файл __u.data__ в каталог __example/data/u.data__ в хранилище по умолчанию вашего кластера. Теперь получить доступ к этим данным можно будет с помощью универсального кода ресурса (URI) __wasb:///example/data/u.data__ из заданий HDInsight.

###Выполнение задания

Используйте следующий сценарий Windows PowerShell для запуска задания, использующего подсистему рекомендаций Mahout на ранее переданном файле __u.data__:

	# The HDInsight cluster name.
	$clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	$jarFile = "file:///C:/apps/dist/mahout-0.9.0.2.1.15.1-1234/examples/target/mahout-examples-0.9.0.2.1.15.1-1234-job.jar"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///example/temp"

	# Create the job definition
	$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
	# Write out any error information
	Write-Host "STDERR"
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE]Задания Mahout не удаляют временные данные, созданные во время выполнения задания. В примере задания указан параметр `--tempDir`, чтобы выделить временные файлы в отдельный каталог.

Задание Mahout не возвращает выходные данные в STDOUT. Вместо этого оно сохраняет их в указанном выходном каталоге как __part-r-00000__. Этот сценарий скачивает данный файл в __output.txt__ в текущем каталоге на рабочей станции.

Ниже приведен пример содержимого такого файла.

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Первый столбец — `userID`. Значения, хранящиеся в скобках «[» и «]», — это `movieId`:`recommendationScore`.

###Просмотр результатов

Сформированный результат может подходить для использования в приложении, хотя и не быть достаточно удобочитаемым. Некоторые файлы, извлеченные ранее в папку __ml-100k__, можно использовать для разрешения `movieId` в название фильма. Именно это делает следующий сценарий PowerShell:

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "u.data"
	        -movieFile "u.item"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

Чтобы использовать этот сценарий, необходимо предварительно извлечь папку __ml-100k__. Ниже приводится пример запуска сценария:

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

Результат должен выглядеть аналогично следующему:

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Классификация данных с использованием командной строки Hadoop

Одним из доступных в Mahout способов классификации является создание [случайного леса][forest]. Это многоэтапный процесс, использующий обучающие данные для создания деревьев принятия решения, которые затем используются для классификации данных. В нем используется класс __org.apache.mahout.classifier.df.tools.Describe__, входящий в состав Mahout. В настоящее время его необходимо выполнять из командной строки Hadoop.

###Загрузка данных

1. Скачайте следующие файлы из [набора данных NSL-KDD](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff) — файл для обучения;

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff) — тестовые данные.

2. Откройте каждый файл и удалите все строки сверху, которые начинаются с "@", а затем сохраните файлы. Если их не удалить, то вы получите сообщения об ошибке при использовании этих данных с Mahout.

2. Передайте файлы в папку __example/data__. Это можно сделать с помощью следующего сценария. Замените __CLUSTERNAME__ именем кластера HDInsight. Замените FILENAME именем отправляемого файла.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###Выполнение задания

1. Это задание выполняется в командной строке Hadoop. Запустите протокол удаленного рабочего стола для кластера HDInsight, а затем выполните подключение, следуя инструкциям раздела [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#rdp).

3. После подключения щелкните значок __Командная строка Hadoop__, чтобы открыть командную строку Hadoop.

	![командная строка hadoop][hadoopcli]

3. C помощью следующей команды сформируйте дескриптор файла (__KDDTrain+.info__) с использованием Mahout.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L` описывает атрибуты данных в файле. Например, L означает метку.

4. Создайте лес деревьев принятия решений с помощью следующей команды:

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Ее выходные данные сохраняются в каталоге __nsl-forest__, который расположен в хранилище для вашего кластера HDInsight по адресу __wasb://user/&lt;username>/nsl-forest/nsl-forest.seq. &lt;username> — это имя пользователя, которое используется для сеанса удаленного рабочего стола. Этот файл имеет машиночитаемый формат.

5. Протестируйте лес путем классификации набора данных __KDDTest+.arff__. Используйте следующую команду:

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    Эта команда возвращает итоговые сведения о классификации, аналогичные следующим:

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  Это задание также создает файл, расположенный в __wasb:///example/data/predictions/KDDTest+.arff.out__. Однако этот файл имеет машиночитаемый формат.

> [AZURE.NOTE]Задания Mahout не перезаписывают существующие файлы. Если вы хотите заново запустить эти задания, то нужно удалить файлы, созданные предыдущими заданиями.

##<a name="troubleshooting"></a>Устранение неполадок

###<a name="install"></a>Установка Mahout

Mahout устанавливается на кластерах HDInsight версии 3.1, а также может устанавливаться вручную на кластерах HDInsight версии 3.0 или 2.1 следующим образом:

1. Используемая версия Mahout зависит от версии HDInsight вашего кластера. Вы можете узнать версию кластера с помощью следующей команды [Azure PowerShell][aps]\:

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * __Для HDInsight 2.1__ можно скачать архив Java (JAR-файл), содержащий [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Для HDInsight 3.0__ вам необходимо [выполнить сборку Mahout из исходного кода][build] и указать версию Hadoop, предоставленную HDInsight. Установите необходимые компоненты, перечисленные на странице сборки, скачайте исходный код и используйте следующие команды для создания JAR-файлов Mahout:

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [AZURE.NOTE] После выпуска Mahout 1.0 появится возможность использовать встроенные пакеты с HDInsight 3.0.

2. Загрузите файл jar в каталог __example/jars__ в хранилище по умолчанию для вашего кластера. Замените CLUSTERNAME в следующем сценарии на имя кластера HDInsight, а FILENAME замените на путь к файлу __mahout-coure-0.9-job.jar__.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###Отсутствие возможности перезаписи файлов

Задания Mahout не удаляют временные файлы, созданные во время обработки. Кроме того, задания не перезаписывают существующий файл результатов.

Во избежание ошибок при запуске заданий Mahout либо удаляйте временные файлы и выходные файлы перед очередным запуском, либо каждый раз используйте уникальные каталоги для временных и выходных файлов.

###Не удается найти JAR-файл

Кластеры HDInsight 3.1 содержат Mahout. Путь и имя файла включают в себя версию Mahout, установленную в кластере. Пример сценария Windows PowerShell в этом учебнике использует путь, который действителен по состоянию на июль 2014 года, однако в будущем он изменится при обновлении HDInsight из-за изменения номера версии. Чтобы определить текущий путь к JAR-файлу Mahout для своего кластера, используйте следующую команду Windows PowerShell, затем внесите изменения в сценарий, указав полученный путь к файлу:

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar'

###<a name="nopowershell"></a>Классы, которые не работают с Windows PowerShell

Задания Mahout, использующие перечисленные ниже классы, будут возвращать разнообразные ошибки при использовании в Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Для запуска заданий, использующих указанные классы, подключитесь к кластеру HDInsight и запустите их, используя командную строку Hadoop. Пример см. в разделе [Классификация данных с использованием командной строки Hadoop](#classify).

##Дальнейшие действия

Теперь, когда вы узнали, как использовать Mahout, откройте для себя другие способы работы с данными в HDInsight:

* [Использование Hive с HDInsight](../hadoop-use-hive.md)
* [Использование Pig с HDInsight](../hadoop-use-pig.md)
* [Использование MapReduce с HDInsight](../hadoop-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: ../hdinsight-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!-----HONumber=Oct15_HO4-->