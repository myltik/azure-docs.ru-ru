---
title: "Создание рекомендаций с использованием Mahout в HDInsight с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как использовать библиотеку машинного обучения Apache Mahout для создания списка рекомендуемых фильмов с помощью HDInsight (Hadoop) из сценария PowerShell, запущенного на клиенте."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: fdca3ed832dc0895a04dc3fda2dcf863d8938d8a
ms.lasthandoff: 01/18/2017


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight (PowerShell)
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Узнайте, как использовать библиотеку машинного обучения [Apache Mahout](http://mahout.apache.org) для создания списка рекомендуемых к просмотру фильмов с помощью Azure HDInsight. В этом документе вы узнаете, как удаленно выполнять Mahout с помощью Azure PowerShell.

Mahout — это библиотека [машинного обучения][ml] для Apache Hadoop. Mahout содержит алгоритмы для обработки данных, такие как фильтрация, классификация и кластеризация. В этой статье описано, как создать список рекомендуемых фильмов на основе фильмов, уже просмотренных вашими друзьями, используя подсистему рекомендаций.

## <a name="prerequisites"></a>Предварительные требования

* Кластер HDInsight под управлением Linux. Дополнительные сведения о создании кластера см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][getstarted].

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **Рабочая станция с Azure PowerShell.**.

    > [!IMPORTANT]
    > Поддержка Azure PowerShell для управления ресурсами HDInsight с помощью диспетчера служб Azure (ASM) объявлена **устаревшей** и будет прекращена с 1 января 2017 г. В описанных в этом документе инструкциях используются новые командлеты HDInsight, которые работают с Azure Resource Manager.
    >
    > Чтобы установить последнюю версию Azure PowerShell, выполните действия из статьи [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). Если у вас есть сценарии, в которые нужно добавить новые командлеты, работающие с Azure Resource Manager, см. статью [Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="recommendations"></a>Создание рекомендаций с использованием Azure PowerShell

> [!NOTE]
> В то время как задание, используемое в этом разделе, работает с Azure PowerShell, многие классы, предоставляемые Mahout, в настоящее время не работают с Azure PowerShell и должны запускаться из командной строки Hadoop. Список классов, которые не работают с Azure PowerShell, приведен в разделе [Устранение неполадок](#troubleshooting).
>
> Пример использования SSH для подключения к HDInsight и выполнения примеров Mahout в кластере см. в статье [Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-mahout-linux-mac.md).

Одной из функций, предоставляемой Mahout, является подсистема рекомендаций. Она принимает данные в формате `userID`, `itemId` и `prefValue` (предпочтения пользователей для элемента). Затем Mahout может провести анализ совместной встречаемости, чтобы определить: *пользователи с предпочтениями к одному элементу также имеют их и к определенным другим элементам*. После этого Mahout определяет пользователей со сходными предпочтениями элементов, что можно использовать для создания рекомендаций.

Ниже приведен предельно простой пример с использованием фильмов:

* **Совместная встречаемость**: Джо, Алисе и Бобу нравятся фильмы *Звездные войны*, *Империя наносит ответный удар* и *Возвращение джедая*. Mahout определяет, что пользователям, которым нравится любой из этих фильмов, также нравятся другие два.

* **Совместная встречаемость**: Бобу и Алисе также нравятся фильмы *Призрачная угроза*, *Атака клонов* и *Месть ситхов*. Mahout определяет, что пользователям, которым нравится предыдущие три фильма, также нравятся и эти три.

* **Рекомендации на основе подобия:**так как Джо понравились первые три фильма, Mahout будет отбирать фильмы, которые нравились другим пользователям со схожими предпочтениями, но которые Джо еще не смотрел (по положительным отзывам и рейтингу). В этом случае Mahout рекомендует посмотреть фильмы *Призрачная угроза*, *Атака клонов* и *Месть ситхов*.

### <a name="understanding-the-data"></a>Основные сведения о данных

Очень кстати, что компания [GroupLens Research][movielens] предоставляет данные о рейтинге фильмов в формате, совместимом с Mahout. Эти данные можно найти в хранилище по умолчанию вашего кластера по адресу `/HdiSamples//HdiSamples/MahoutMovieData`.

Там есть два файла, `moviedb.txt` (сведения о фильмах) и `user-ratings.txt`. Файл user-ratings.txt используется во время анализа, а moviedb.txt используется для отображения понятного текста в результатах анализа.

Данные, содержащиеся файле user-ratings.txt, имеют структуру `userID`, `movieID`, `userRating` и `timestamp`, благодаря чему мы видим, насколько высоко каждый пользователь оценил фильм. Вот пример данных:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Выполнение задания

Используйте следующий сценарий Windows PowerShell для запуска задания, использующего подсистему рекомендаций Mahout с данными о фильмах.

> [!NOTE]
> Этот файл запрашивает сведения, используемые для подключения к кластеру HDInsight и выполнения заданий. Для завершения заданий и скачивания файла output.txt может потребоваться несколько минут.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster (the default name is usually 'admin')"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Use Hive to figure out the path to the mahout examples
#Because the file name/path has a version number in it that changes
$queryString = "!ls /usr/hdp/current/mahout-client"
$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString
$hiveJob=Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -HttpCredential $creds
$dummy = wait-azurermhdinsightjob -ClusterName $clusterName -JobId $hiveJob.JobId -HttpCredential $creds
#Get the files returned from Hive
$files=get-azurermhdinsightjoboutput -clustername $clusterName -JobId $hiveJob.JobId -DefaultContainer $container -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -HttpCredential $creds
#Find the file that starts with mahout-examples and ends in job.jar
$jarFile = $files | select-string "mahout-examples.+job\.jar" | % {$_.Matches.Value}
#Add the full path
$jarFile = "file:///usr/hdp/current/mahout-client/$jarFile"

# The arguments for the mahout job
# * input - the path to the data uploaded to HDInsight
# * output - the path to store output data
# * tempDir - the directory for temp files
$jobArguments = "-s", "SIMILARITY_COOCCURRENCE", `
                "--input", "/HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt",
                "--output", "/example/out",
                "--tempDir", "/example/temp"

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

# Write out any error information
Write-Host "STDERR"
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError

# Download the output
Get-AzureStorageBlobContent `
        -Blob example/out/part-r-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context
#Download movie and user files for use in displaying results
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/moviedb.txt" `
        -Container $container `
        -Destination moviedb.txt `
        -Context $context
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt" `
        -Container $container `
        -Destination user-ratings.txt `
        -Context $context
```

> [!NOTE]
> Задания Mahout не удаляют временные данные, созданные во время выполнения задания. В примере задания указан параметр `--tempDir` , чтобы выделить временные файлы в отдельный каталог.

Задание Mahout не возвращает выходные данные в STDOUT. Вместо этого оно сохраняет их в указанном выходном каталоге как **part-r-00000**. Этот сценарий скачивает данный файл в **output.txt** в текущем каталоге на рабочей станции.

Ниже приведен пример содержимого такого файла.

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Первый столбец — `userID`. Значения, хранящиеся в скобках «[» и «]», — это `movieId`:`recommendationScore`.

Сценарий также скачивает файлы `moviedb.txt` и `user-ratings.txt`, необходимые, чтобы преобразовать выходные данные в удобочитаемый формат.

### <a name="view-the-output"></a>Просмотр результатов

Сформированный результат может подходить для использования в приложении, хотя и не быть достаточно удобочитаемым. Файл `moviedb.txt` с сервера может использоваться для разрешения `movieId` в названии фильма. Используйте сценарий PowerShell для отображения рекомендаций с названиями фильмов:

```powershell
<#
.SYNOPSIS
    Displays recommendations for movies.
.DESCRIPTION
    Displays recommendations generated by Mahout
    with HDInsight example in a human readable format.
.EXAMPLE
    .\Show-Recommendation -userId 4
        -userDataFile "user-ratings.txt"
        -movieFile "moviedb.txt"
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
```

Ниже приводится пример запуска сценария:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

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

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="cannot-overwrite-files"></a>Отсутствие возможности перезаписи файлов

Задания Mahout не удаляют временные файлы, созданные во время обработки. Кроме того, задания не перезаписывают существующий файл результатов.

Во избежание ошибок при запуске заданий Mahout либо удаляйте временные файлы и выходные файлы перед очередным запуском, либо каждый раз используйте уникальные каталоги для временных и выходных файлов. Используйте следующий сценарий PowerShell, чтобы удалить файлы, созданные ранее в этом документе.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard, 
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Классы, которые не работают с Azure PowerShell

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

Для запуска заданий, использующих указанные классы, подключитесь к кластеру HDInsight по протоколу SSH и запустите их, используя командную строку. Например, сведения об использовании SSH для выполнения заданий Mahout см. в статье [Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать Mahout, откройте для себя другие способы работы с данными в HDInsight:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

