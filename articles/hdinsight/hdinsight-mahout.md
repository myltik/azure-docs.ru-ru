---
title: Создание рекомендаций с использованием Mahout в HDInsight с помощью PowerShell (Azure) | Документация Майкрософт
description: Узнайте, как использовать библиотеку машинного обучения Apache Mahout для создания списка рекомендуемых фильмов с помощью HDInsight (Hadoop) из сценария PowerShell, запущенного на клиенте.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 49a092ee23b79c483aa7bbd8b3d5150e909b6884
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177358"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Узнайте, как использовать библиотеку машинного обучения [Apache Mahout](http://mahout.apache.org) для создания списка рекомендуемых к просмотру фильмов с помощью Azure HDInsight. В примере в этом документе для запуска заданий Mahout используется Azure PowerShell.

## <a name="prerequisites"></a>предварительным требованиям

* Кластер HDInsight под управлением Linux. Дополнительные сведения о создании кластера см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][getstarted].

    > [!IMPORTANT]
    > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Создание рекомендаций с использованием Azure PowerShell

> [!WARNING]
> Задание в этом разделе работает с использованием Azure PowerShell. В настоящее время многие классы, предоставляемые Mahout, не работают с Azure PowerShell. Список классов, которые не работают с Azure PowerShell, приведен в разделе [Устранение неполадок](#troubleshooting).
>
> Пример использования SSH для подключения к HDInsight и выполнения примеров Mahout в кластере см. в статье [Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Linux](hadoop/apache-hadoop-mahout-linux-mac.md).

Одной из функций, предоставляемой Mahout, является подсистема рекомендаций. Она принимает данные в формате `userID`, `itemId` и `prefValue` (предпочтения пользователей для элемента). С помощью этих данных Mahout определяет пользователей со сходными предпочтениями элементов, что можно использовать для создания рекомендаций.

Ниже приведено упрощенное пошаговое руководство по процессу рекомендаций с примером.

* **Совместная встречаемость**: Джо, Алисе и Бобу нравятся фильмы *Звездные войны*, *Империя наносит ответный удар* и *Возвращение джедая*. Mahout определяет, что пользователям, которым нравится любой из этих фильмов, также нравятся другие два.

* **Совместная встречаемость**: Бобу и Алисе также нравятся фильмы *Призрачная угроза*, *Атака клонов* и *Месть ситхов*. Mahout определяет, что пользователям, которым нравится предыдущие три фильма, также нравятся и эти фильмы.

* **Рекомендации на основе подобия:** так как Джо понравились первые три фильма, Mahout будет отбирать фильмы, которые нравились другим пользователям со схожими предпочтениями, но которые Джо еще не смотрел (по положительным отзывам и рейтингу). В этом случае Mahout рекомендует посмотреть фильмы *Призрачная угроза*, *Атака клонов* и *Месть ситхов*.

### <a name="understanding-the-data"></a>Основные сведения о данных

Компания [GroupLens Research][movielens] предоставляет данные о рейтинге фильмов в формате, совместимом с Mahout. Эти данные можно найти в хранилище по умолчанию вашего кластера по адресу `/HdiSamples/HdiSamples/MahoutMovieData`.

В нем есть два файла, `moviedb.txt` (сведения о фильмах) и `user-ratings.txt`. Файл `user-ratings.txt` используется во время анализа. А файл `moviedb.txt` используется для составления понятного текста при отображении результатов анализа.

Данные, содержащиеся в файле user-ratings.txt, имеют структуру `userID`, `movieID`, `userRating` и `timestamp`, благодаря чему можно увидеть, насколько высоко каждый пользователь оценил фильм. Вот пример данных:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Выполнение задания

Используйте следующий сценарий Windows PowerShell для запуска задания, использующего подсистему рекомендаций Mahout с данными о фильмах.

> [!NOTE]
> Этот файл запрашивает сведения, используемые для подключения к кластеру HDInsight и выполнения заданий. Для завершения заданий и скачивания файла output.txt может потребоваться несколько минут.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

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

Сформированный результат может подходить для использования в приложении, хотя и не быть достаточно понятным. Файл `moviedb.txt` с сервера может использоваться для разрешения `movieId` в названии фильма. Используйте сценарий PowerShell для отображения рекомендаций с названиями фильмов:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Используйте следующую команду для отображения рекомендаций в понятном формате. 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

Результат будет аналогичен приведенному ниже:

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

Задания Mahout не удаляют временные файлы, созданные во время обработки. Кроме того, задания не перезаписывают существующий выходной файл.

Во избежание ошибок при запуске заданий Mahout удаляйте временные файлы и выходные файлы перед очередным запуском. Используйте следующий сценарий PowerShell, чтобы удалить файлы, созданные ранее в этом документе предыдущими сценариями.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
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

Задания Mahout, использующие перечисленные ниже классы, будут возвращать разнообразные ошибки при использовании в Windows PowerShell.

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

Для запуска заданий, использующих указанные классы, подключитесь к кластеру HDInsight по протоколу SSH и запустите их, используя командную строку. Например, сведения об использовании SSH для выполнения заданий Mahout см. в статье [Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Linux](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как использовать Mahout, откройте для себя другие способы работы с данными в HDInsight:

* [Использование Hive с HDInsight](hadoop/hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hadoop/hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
