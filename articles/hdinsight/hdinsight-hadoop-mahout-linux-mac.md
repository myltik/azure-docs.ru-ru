---
title: Создание рекомендаций с помощью HDInsight на основе Mahout и Linux | Microsoft Docs
description: Узнайте, как использовать библиотеку машинного обучения Apache Mahout для создания списка рекомендуемых фильмов с помощью HDInsight (Hadoop) на платформе Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: larryfr

---
# Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Linux
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Узнайте, как использовать библиотеку машинного обучения [Apache Mahout](http://mahout.apache.org) для создания списка рекомендуемых к просмотру фильмов с помощью Azure HDInsight.

Mahout — это библиотека [машинного обучения][ml] для Apache Hadoop. Mahout содержит алгоритмы для обработки данных, такие как фильтрация, классификация и кластеризация. В этой статье вы будете использовать подсистему рекомендаций для создания рекомендаций к просмотру на основе фильмов, уже просмотренных вашими друзьями.

> [!NOTE]
> Действия, описанные в этом документе, требуют Hadoop в кластере HDInsight на платформе Linux. Сведения об использовании Mahout с кластерами на базе Windows см. в разделе [Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Windows](hdinsight-mahout.md)
> 
> 

## Предварительные требования
* Hadoop в кластере HDInsight на платформе Linux. Для получения сведений о создании кластера см. раздел [Начало работы с Hadoop в HDInsight на платформе Linux][getstarted].

## Управление версиями Mahout
Дополнительные сведения о версии Mahout, входящий в состав кластера HDInsight, см. в разделе [Версии HDInsight и компоненты Hadoop](hdinsight-component-versioning.md).

> [!WARNING]
> Хотя в кластер HDInsight можно передать другую версию Mahout, только компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба поддержки Майкрософт поможет вам выявить и устранить проблемы, связанные с этими компонентами.
> 
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/ru-RU/home?forum=hdinsight) и [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты по адресу [http://apache.org](http://apache.org), например для [Hadoop](http://hadoop.apache.org/) и [Spark](http://spark.apache.org/).
> 
> 

## <a name="recommendations"></a>Общие сведения о рекомендациях
Одной из функций, предоставляемой Mahout, является подсистема рекомендаций. Она принимает данные в формате `userID`, `itemId` и `prefValue` (предпочтения пользователей для элемента). Затем Mahout может провести анализ совместной встречаемости, чтобы определить: *пользователи с предпочтениями к одному элементу также имеют их и к определенным другим элементам*. После этого Mahout определяет пользователей со сходными предпочтениями элементов, что можно использовать для создания рекомендаций.

Ниже приведен предельно простой пример с использованием фильмов:

* **Совместная встречаемость:** Джо, Алисе и Бобу нравятся *Звездные войны*, *Империя наносит ответный удар* и *Возвращение джедая*. Mahout определяет, что пользователям, которым нравится любой из этих фильмов, также нравятся другие два.
* **Совместная встречаемость:** Бобу и Алисе также нравятся *Призрачная угроза*, *Атака клонов* и *Месть ситхов*. Mahout определяет, что пользователям, которым нравится предыдущие три фильма, также нравятся и эти три.
* **Рекомендации на основе подобия:** так как Джо понравились первые три фильма, Mahout будет отбирать фильмы, которые нравились другим пользователям со схожими предпочтениями, но которые Джо еще не смотрел (по положительным отзывам и рейтингу). В этом случае Mahout рекомендует посмотреть *Призрачную угрозу*, *Атаку клонов* и *Месть ситхов*.

### Основные сведения о данных
Очень кстати, что компания [GroupLens Research][movielens] предоставляет данные о рейтинге фильмов в формате, совместимом с Mahout. Эти данные можно найти в хранилище по умолчанию вашего кластера по адресу `/HdiSamples/HdiSamples/MahoutMovieData`.

Там есть два файла, `moviedb.txt` (сведения о фильмах) и `user-ratings.txt`. Файл user-ratings.txt используется во время анализа, а moviedb.txt используется для отображения понятного текста в результатах анализа.

Данные, содержащиеся файле user-ratings.txt, имеют структуру `userID`, `movieID`, `userRating` и `timestamp`, благодаря чему мы видим, насколько высоко каждый пользователь оценил фильм. Вот пример данных:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## Выполнение анализа
Чтобы запустить задание рекомендации, выполните следующую команду:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [!NOTE]
> Выполнение задания может занять несколько минут; может выполняться несколько заданий MapReduce.
> 
> 

## Просмотр результатов
1. По завершении задания воспользуйтесь следующей командой, чтобы просмотреть результат:
   
        hdfs dfs -text /example/data/mahoutout/part-r-00000
   
    Результат будет выглядеть следующим образом:
   
        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
   
    Первый столбец — `userID`. Значения, хранящиеся в '[' and ']' — это `movieId`:`recommendationScore`.
2. Выходные данные, а также moviedb.txt, можно использовать для отображения для пользователей более понятных сведений. Во-первых, необходимо скопировать файлы локально с помощью следующих команд.
   
        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
   
    Выходные данные будут скопированы в файл **recommendations.txt** в текущем каталоге. В него же будут скопированы файлы данных фильмов.
3. Чтобы создать новый скрипт Python, который будет искать названия фильмов для данных в полученных рекомендациях, используйте следующую команду:
   
        nano show_recommendations.py
   
    Когда откроется редактор, используйте следующее в качестве содержимого файла:
   
        #!/usr/bin/env python
   
        import sys
   
        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)
   
        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]
   
        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()
   
        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()
   
        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()
   
        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"
   
        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"
   
    Нажмите **Ctrl-X**, **Y**, и, наконец, **ВВОД** для сохранения данных.
4. Используйте следующую команду, чтобы сделать файл исполняемым:
   
        chmod +x show_recommendations.py
5. Запустите скрипт Python. Далее предполагается, что вы находитесь в каталоге, куда были скачаны все файлы.
   
        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
   
    Он рассмотрит рекомендации, созданные для пользователя ID 4.
   
   * Файл **user-ratings.txt** используется для получения фильмов, которые оценил пользователь.
   * Файл **moviedb.txt** используется для извлечения названий фильмов.
   * Файл **recommendations.txt** используется для получения рекомендованных фильмов для этого пользователя
     
     Результат этой команды будет аналогичен следующему:
     
       Reading Movies Descriptions
       Reading Rated Movies
       Reading Recommendations
     
     ##   Rated Movies
       Mimic (1997), rating=3
       Ulee's Gold (1997), rating=5
       Incognito (1997), rating=5
       One Flew Over the Cuckoo's Nest (1975), rating=4
       Event Horizon (1997), rating=4
       Client, The (1994), rating=3
       Liar Liar (1997), rating=5
       Scream (1996), rating=4
       Star Wars (1977), rating=5
       Wedding Singer, The (1998), rating=5
       Starship Troopers (1997), rating=4
       Air Force One (1997), rating=5
       Conspiracy Theory (1997), rating=3
       Contact (1997), rating=5
       Indiana Jones and the Last Crusade (1989), rating=3
       Desperate Measures (1998), rating=5
       Seven (Se7en) (1995), rating=4
       Cop Land (1997), rating=5
       Lost Highway (1997), rating=5
       Assignment, The (1997), rating=5
       Blues Brothers 2000 (1998), rating=5
       Spawn (1997), rating=2
       Wonderland (1997), rating=5
     
     ##   In & Out (1997), rating=5
     ##   Recommended Movies
       Seven Years in Tibet (1997), score=5.0
       Indiana Jones and the Last Crusade (1989), score=5.0
       Jaws (1975), score=5.0
       Sense and Sensibility (1995), score=5.0
       Independence Day (ID4) (1996), score=5.0
       My Best Friend's Wedding (1997), score=5.0
       Jerry Maguire (1996), score=5.0
       Scream 2 (1997), score=5.0
       Time to Kill, A (1996), score=5.0
     
     ##   Rock, The (1996), score=5.0

## Удаление временных данных
Задания Mahout не удаляют временные данные, созданные во время выполнения задания. В примере задания указан параметр `--tempDir`, чтобы выделить временные файлы в отдельный каталог для простоты последующего их удаления. Чтобы удалить временные файлы, используйте следующую команду:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [!WARNING]
> Если вы хотите выполнить команду еще раз, необходимо удалить выходной каталог. Используйте следующую команду для удаления этого каталога:
> 
> ```hdfs dfs -rm -f -r /example/data/mahoutout```
> 
> 

## Дальнейшие действия
Теперь, когда вы узнали, как использовать Mahout, откройте для себя другие способы работы с данными в HDInsight:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
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


<!---HONumber=AcomDC_0914_2016-->