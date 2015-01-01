<properties title="Generate movie recommendations using Mahout" pageTitle="Создание списка рекомендуемых к просмотру фильмов с использованием Mahout с Microsoft Azure HDInsight (Hadoop)" description="Узнайте, как использовать библиотеку машинного обучения Apache Mahout для создания списка рекомендуемых к просмотру фильмов с HDInsight (Hadoop)" metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

#Создание списка рекомендуемых к просмотру фильмов с использованием Apache Mahout с HDInsight (Hadoop)

Узнайте, как использовать библиотеку машинного обучения [Apache Mahout](http://mahout.apache.org) для создания списка рекомендуемых к просмотру фильмов с Microsoft Azure HDInsight (Hadoop).

> [WACOM.NOTE] Для использования информации, приведенной в этой статье, вам понадобится кластер HDInsight. Для получения информации о создании кластера см. раздел [Начало работы с Hadoop в HDInsight][getstarted].
>
> Библиотека Mahout предустановлена в кластерах HDInsight 3.1. Если вы используете более раннюю версию HDInsight, перед тем как продолжить, см. раздел [Установка Mahout](#install) .

##<a name="learn"></a>О чем вы узнаете?

Mahout - это библиотека [машинного обучения][ml] для Apache Hadoop. Mahout содержит алгоритмы для обработки данных, такие как фильтрация, классификация и кластеризация. В этой статье вы будет использовать подсистему рекомендаций для создания рекомендаций для просмотра фильмов на основе уже просмотренных вашими друзьями. Вы также узнаете о том, как выполнять классификацию с помощью леса деревьев принятия решений. Вы изучите следующее:

* - как запускать задания Mahout из PowerShell;

* - как запускать задания Mahout из командной строки Hadoop;

* - как устанавливать Mahout на кластерах HDInsight версий 2.0 и 3.0.

##Содержание

* [Создание рекомендаций с использованием PowerShell](#recommendations)
* [Классификация данных с использованием командной строки Hadoop](#classify)
* [Устранение неполадок](#troubleshooting)


##<a name="recommendations"></a>Создание рекомендаций с использованием PowerShell

> [WACOM.NOTE] В то время как задание, используемое в этом разделе, работает с PowerShell, многие классы, идущие в составе Mahout, в настоящее время не работают с PowerShell и должны запускаться из командной строки Hadoop. Список классов, которые не работают с PowerShell, приведен в разделе [Устранение неполадок](#troubleshooting) .
>
> Для примера использования командной строки Hadoop для запуска заданий Mahout, см. раздел [Классификация данных с использованием командной строки Hadoop](#classify).

Одной из функций, предоставляемой Mahout, является подсистема рекомендаций. Она принимает данные в формате userID, itemId, prefValue (предпочтения пользователей для элемента). Затем Mahout может провести анализ совместной встречаемости для определения, что пользователи, имеющие предпочтения к одному элементу, также имеют их и к определенным другим. После этого Mahout определит пользователей со сходными предпочтениями элементов, что можно использовать для создания рекомендаций.

Ниже приведен предельно простой пример с использованием фильмов.

* Совместная встречаемость: Джо, Алисе и Бобу нравятся "Звездные войны", "Империя наносит ответный удар" и "Возвращение Джедая". Mahout определит, что пользователям, которым нравится любой из этих фильмов, также нравятся другие два.

* Совместная встречаемость: Бобу и Алисе также нравятся "Призрачная угроза", "Атака клонов" и "Месть ситхов". Mahout определит, что пользователям, которым нравится предыдущие три фильма, также нравятся и эти три.

* Рекомендации на основе подобия: так как Джо понравились первые три фильма, Mahout будет отбирать фильмы, которые нравились другим пользователям со схожими предпочтениями, но которые Джо еще не смотрел (по положительным отзывам/рейтингу). В этом случае Mahout порекомендует посмотреть "Призрачную угрозу", "Атаку клонов" и "Месть ситхов".

###Загрузка данных

Очень кстати, компания GroupLens Research предоставляет [данные о рейтинге фильмов][movielens] в формате, совместимом с Mahout.

1. Скачайте архив [MovieLens 100k][100k], в котором содержится 100 000 рейтинговых оценок от 1 000 пользователей по 1 700 фильмам.

2. Распакуйте архив. В нем должен находиться каталог ml-100k, содержащий множество файлов с данными, имеющих префикс "u". Файл, который будет анализироваться Mahout, называется u.data. Структура данных этого файла - userID, movieID, userRating и timestamp. Вот пример данных.


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. Загрузите файл u.data в каталог example/data/u.data на вашем кластере HDInsight. Если у вас имеется [Azure PowerShell][aps], то для передачи файла вы можете использовать модуль PowerShell [HDInsight-Tools][tools]. Другие способы передачи файлов можно найти в документе [Отправка данных для заданий Hadoop в HDInsight][upload]. Ниже приведен пример использования Add-HDInsightFile для передачи файла

    	PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "имя вашего кластера"

    Файл u.data будет загружен в каталог example/data/u.data в хранилище по умолчанию вашего кластера. Доступ к нему из заданий HDInsight можно получить с помощью универсального кода ресурса (URI) wasb:///example/data/u.data.

###Выполнение задания

Используйте следующий сценарий PowerShell для запуска задания с подсистемой рекомендаций Mahout на ранее переданном файле u.data.

	# The HDInsight cluster name.
	$clusterName = "the cluster name"

	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	# So dynamically grab it using Hive.
	$mahoutPath = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar' | where {$_.startswith("C:\apps\dist")}
	$jarFile = "file:///$mahoutPath"
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
	$jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///temp/mahout"

	# Create the job definition
	$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job

	# Write out any error information
	Write-Host "STDERR"
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] Задания Mahout не удаляют временные данные, созданные во время выполнения задания. Поэтому в примере задания указан параметр --tempDir, чтобы изолировать временные файлы в отдельный каталог для простоты последующего их удаления.
>
> Для удаления этих файлов можно использовать одну из служебных программ, упоминавшихся в документе [Отправка данных для заданий Hadoop в HDInsight][upload]. Либо можно использовать функцию Remove-HDInsightFile из сценария PowerShell [HDInsight-Tools][tools].
>
> Если не удалить временные файлы или файл с результатами, то вы получите сообщение об ошибке при повторном запуске задания.

Задание Mahout не возвращает результаты в поток STDOUT, а хранит их в указанном каталоге результатов вида part-r-00000. Чтобы скачать и просмотреть файл, используйте функцию Get-HDInsightFile модуля PowerShell [HDInsight-Tools][tools].

Ниже приведен пример содержимого файла:

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Первый столбец - это userID. Значения, хранящиеся в скобках [ и ] - это movieId:recommendationScore.

###Просмотр результатов

Сформированный результат может подходить для использования в приложении, хотя и не быть достаточно читабельным. Некоторые извлеченные ранее в папку ml-100k файлы можно использовать для преобразования movieId в название фильма. В папке ml-100k уже имеется сценарий на Python, который выполняет эту задачу (show_recommendations.py,), вы также можете использовать следующий сценарий PowerShell.

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

Для использования этого сценария нужна ранее извлеченная папка ml-100k, а также локальная копия файла результатов part-r-00000, созданного заданием Mahout. Ниже приводится пример запуска скрипта.

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt


> [WACOM.NOTE] Пример сценария Python, show_recommendations.py, имеет те же параметры запуска.

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

Одним из доступных в Mahout способов классификации является создание [случайного леса][forest]. Это многоэтапный процесс, использующий данные обучения для создания деревьев принятия решения, которые затем используются для классификации данных. Используется класс org.apache.mahout.classifier.df.tools.Describe, входящий в состав Mahout; в настоящее время он должен запускаться из командной строки Hadoop.

###Загрузка данных

Текущая реализация Mahout совместима с форматом репозитория Калифорнийского университета в Ирвине (UCI) [почему это имеет значение, что это за формат]

1. Скачайте следующие файлы с сайта [http://nsl.cs.unb.ca/NSL-KDD/](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff) - тренировочный файл.

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff) - тестовые данные.

2. Откройте каждый файл и удалите все строки сверху, которые начинаются с "@", а затем сохраните файлы. Если этого не сделать, то вы получите сообщения об ошибке при использовании этих данных с Mahout.

2. Передайте файл в папку example/data. Вы также можете использовать функцию Add-HDInsightFile из модуля PowerShell [HDInsight-Tools][tools].

###Выполнение задания

1. Так как задание требует использования командной строки Hadoop, вам предварительно необходимо разрешить подключение к удаленному рабочему столу на [портале управления Azure][management]. На портале выберите ваш кластер HDInsight, затем выберите флажок "Включить удаленный рабочий стол" в нижней части страницы "Конфигурация".

    ![enable remote][enableremote]

    Когда появится запрос на ввод учетных данных, введите имя пользователя и пароль для использования в сеансах подключения к удаленному рабочему столу.

2. После включения доступа к удаленному рабочему столу выберите "Подключить" для начала подключения. После этого будет скачан RDP-файл, который можно использовать для запуска сеанса подключения к удаленному рабочему столу.

    ![connect][connect]

3. После подключения используйте значок командной строки Hadoop, чтобы запустить командную строку Hadoop.

	![hadoop cli][hadoopcli]

3. C помощью следующей команды сформируйте файловый дескриптор (KDDTrain+.info,) с использованием Mahout.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	N 3 C 2 N C 4 N C 8 N 2 C 19 N L - описывает атрибуты данных в файле. Один числовой атрибут, 2 категорийных, и т. д. L обозначает метку.

4. Создайте лес деревьев принятия решений с помощью следующей команды.

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Результат ее выполнения сохраняется в каталоге nsl-forest, который расположен в хранилище для вашего кластера HDInsight по адресу wasb://user/&lt;username>/nsl-forest/nsl-forest.seq. &lt;username> - это имя пользователя, которое используется для сеанса удаленного рабочего стола. Этот файл имеет машиночитаемый формат.

5. Протестируйте лес с помощью классификации набора данных KDDTest+.arff, используя следующую команду.

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

  Это задание также создает файл, расположенный по адресу wasb:///example/data/predictions/KDDTest+.arff.out, однако он имеет машиночитаемый формат.

> [WACOM.NOTE] Задания Mahout не перезаписывают существующие файлы. Если вы хотите заново запустить эти задания, то нужно удалить созданные предыдущими заданиями файлы.

##<a name="troubleshooting"></a>Устранение неполадок

###<a name="install"></a>Установка Mahout

Mahout установлена на кластере HDInsight версии 3.1, может устанавливаться вручную на кластере версии 3.0 или 2.1 с помощью следующих шагов.

1. Используемая версия Mahout зависит от версии HDInsight вашего кластера. Вы можете узнать версию кластера с помощью следующей команды в [Azure PowerShell][aps]:

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * Для HDInsight версии 2.1 можно загрузить JAR-файл, содержащий [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * Для HDInsight версии 3.0 вам необходимо [выполнить сборку Mahout из исходных кодов][build] и указать версию Hadoop, предоставленную HDInsight. Установите необходимые компоненты, перечисленные на странице сборки, загрузите исходный код и используйте следующие команды для создания файла jar с Mahout:

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	После завершения сборки JAR-файл будет создан по адресу mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar.

    	> [WACOM.NOTE] После выхода Mahout 1.0 вы сможете использовать предварительно созданные пакеты с HDInsight 3.0.

2. Передайте JAR-файл в каталог example/jars в хранилище по умолчанию для вашего кластера. Следующий пример использует сценарий [send-hdinsight][sendhdinsight] для передачи файла.

    	PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

###Отсутствие возможности перезаписи файлов

Задания Mahout не удаляют временные файлы, созданные во время обработки. Кроме того, задания не перезаписывают существующий файл результатов.

Во избежание ошибок при запуске заданий Mahout либо удаляйте временные файлы и файл результатов перед очередным запуском, либо используйте разные каталоги для временных файлов и результатов.

###Невозможно найти файл jar

В то время как в состав HDInsight версии 3.1. уже входит Mahout, путь и имя файла включают версию установленной в кластере Mahout. Пример скрипта PowerShell в этом учебнике использует путь, который актуален по состоянию на июль 2014 года, однако в будущем он изменится при обновлении HDInsight из-за изменения номера версии. Чтобы определить текущий путь к файлу jar Mahout для вашего кластера, используйте следующие команды PowerShell, затем внесите изменения в скрипт, указав правильный путь к файлу.

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Классы, которые не работают с PowerShell

Задания Mahout, использующие ниже перечисленные классы, будут возвращать разнообразные ошибки при использовании с PowerShell.

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

Для запуска заданий, использующих указанные классы, подключитесь к кластеру HDInsight и запустите их, используя командную строку Hadoop. Пример см. в разделе [Классификация данных с использованием командной строки Hadoop](#classify) .


[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-get-started/
[upload]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-upload-data/
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

<!--HONumber=35_1-->
