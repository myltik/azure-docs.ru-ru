<properties
	pageTitle="Создание рекомендаций с помощью Mahout и Hadoop | Microsoft Azure"
	description="Узнайте, как использовать библиотеку машинного обучения Apache Mahout для создания списка рекомендуемых фильмов с помощью HDInsight (Hadoop) на платформе Linux."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="larryfr"/>

#Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Linux (предварительная версия)

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Узнайте, как использовать библиотеку машинного обучения [Apache Mahout](http://mahout.apache.org) для создания списка рекомендуемых к просмотру фильмов с помощью Azure HDInsight.

Mahout — это библиотека [машинного обучения][ml] для Apache Hadoop. Mahout содержит алгоритмы для обработки данных, такие как фильтрация, классификация и кластеризация. В этой статье вы будете использовать подсистему рекомендаций для создания рекомендаций к просмотру на основе фильмов, уже просмотренных вашими друзьями.

> [AZURE.NOTE]Действия, описанные в этом документе, требуют Hadoop в кластере HDInsight на платформе Linux (предварительная версия). Сведения об использовании Mahout с кластерами на базе Windows см. в разделе [Создание списка рекомендуемых фильмов с помощью Apache Mahout и Hadoop в HDInsight на платформе Windows](hdinsight-mahout.md)

##Предварительные требования

* Hadoop в кластере HDInsight на платформе Linux. Для получения сведений о создании кластера см. раздел [Начало работы с Hadoop в HDInsight на платформе Linux][getstarted].

##<a name="recommendations"></a>Общие сведения о рекомендациях

Одной из функций, предоставляемой Mahout, является подсистема рекомендаций. Она принимает данные в формате `userID`, `itemId` и `prefValue` (предпочтения пользователей для элемента). Затем Mahout может провести анализ совместной встречаемости, чтобы определить: _пользователи с предпочтениями к одному элементу также имеют их и к определенным другим элементам_. После этого Mahout определяет пользователей со сходными предпочтениями элементов, что можно использовать для создания рекомендаций.

Ниже приведен предельно простой пример с использованием фильмов:

* __Совместная встречаемость:__ Джо, Алисе и Бобу нравятся _Звездные войны_, _Империя наносит ответный удар_ и _Возвращение джедая_. Mahout определяет, что пользователям, которым нравится любой из этих фильмов, также нравятся другие два.

* __Совместная встречаемость:__ Бобу и Алисе также нравятся _Призрачная угроза_, _Атака клонов_ и _Месть ситхов_. Mahout определяет, что пользователям, которым нравится предыдущие три фильма, также нравятся и эти три.

* __Рекомендации на основе подобия:__ так как Джо понравились первые три фильма, Mahout будет отбирать фильмы, которые нравились другим пользователям со схожими предпочтениями, но которые Джо еще не смотрел (по положительным отзывам и рейтингу). В этом случае Mahout рекомендует посмотреть _Призрачную угрозу_, _Атаку клонов_ и _Месть ситхов_.

##Загрузка данных

Очень кстати, что компания [GroupLens Research][movielens] предоставляет данные о рейтинге фильмов в формате, совместимом с Mahout. Выполните следующие действия для скачивания данных, а затем загрузите их в хранилище по умолчанию для кластера:

1. Используйте SSH для подключения к кластеру HDInsight под управлением Linux. Адрес, используемый при подключении: `CLUSTERNAME-ssh.azurehdinsight.net`, порт: `22`.

	Дополнительные сведения об использовании SSH для подключения к HDInsight см. в следующих документах:

    * **Клиенты Linux, Unix или OS X**: в разделе [Подключение к кластеру HDInsight под управлением Linux из Linux, OS X или Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Клиенты Windows**: в разделе [Подключение к кластеру HDInsight под управлением Linux из Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

2. Скачайте архив MovieLens 100k, который включает 100 000 рейтинговых оценок от 1000 пользователей по 1700 фильмам.

        curl -O http://files.grouplens.org/datasets/movielens/ml-100k.zip

3. Извлеките архив с помощью следующей команды:

        unzip ml-100k.zip

    Эта команда извлечет содержимое в новую папку с именем **ml-100k**.

4. Используйте следующие команды для копирования данных в хранилище HDInsight:

        cd ml-100k
        hadoop fs -copyFromLocal ml-100k/u.data /example/data/u.data

    Данные, содержащиеся в этом файле, имеют структуру `userID`, `movieID`, `userRating` и `timestamp`, благодаря чему мы видим, насколько высоко каждый пользователь оценил фильм. Вот пример данных:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596

##Выполнение задания

Чтобы запустить задание рекомендации, выполните следующую команду:

	mahout recommenditembased -s SIMILARITY_COOCCURRENCE --input /example/data/u.data --output /example/data/mahoutout  --tempDir /temp/mahouttemp

> [AZURE.NOTE]Выполнение задания может занять несколько минут; может выполняться несколько заданий MapReduce.

##Просмотр результатов

1. По завершении задания воспользуйтесь следующей командой, чтобы просмотреть результат:

		hadoop fs -text /example/data/mahoutout/part-r-00000

	Результат будет выглядеть следующим образом:

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	Первый столбец — `userID`. Значения, хранящиеся в '[' and ']' — это `movieId`:`recommendationScore`.

2. Для удобства чтения могут быть использованы некоторые данные, содержащиеся в каталоге **ml-100k**. Сначала скачайте данные с помощью следующей команды:

		hadoop fs -copyToLocal /example/data/mahoutout/part-r-00000 recommendations.txt

	При этом выходные данные будут скопированы в файл с именем **recommendations.txt** в текущем каталоге.

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

3. Используйте следующую команду, чтобы сделать файл исполняемым:

		chmod +x show_recommendations.py

4. Запустите скрипт Python.

		./show_recommendations.py 4 ml-100k/u.data ml-100k/u.item recommendations.txt

	Он рассмотрит рекомендации, созданные для пользователя ID 4.

	* Файл **u.data** используется для получения фильмов, которые оценил пользователь
	* Файл **u.item** используется для извлечения названий фильмов
	* Файл **recommendations.txt** используется для получения рекомендованных фильмов для этого пользователя

	Результат этой команды будет аналогичен следующему:

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
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
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##Удаление временных данных

Задания Mahout не удаляют временные данные, созданные во время выполнения задания. В примере задания указан параметр `--tempDir`, чтобы выделить временные файлы в отдельный каталог для простоты последующего их удаления. Чтобы удалить временные файлы, используйте следующую команду:

	hadoop fs -rm -f -r wasb:///temp/mahouttemp

> [AZURE.WARNING]Если не удалить временные файлы или выходной файл, то при повторном запуске задания с тем же путем `--tempDir` вы получите сообщение об ошибке "не удается перезаписать файл".

##Дальнейшие действия

Теперь, когда вы узнали, как использовать Mahout, откройте для себя другие способы работы с данными в HDInsight:

* [Использование Hive с HDInsight](../hadoop-use-hive.md)
* [Использование Pig с HDInsight](../hadoop-use-pig.md)
* [Использование MapReduce с HDInsight](../hadoop-use-mapreduce.md)

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
 

<!---HONumber=July15_HO2-->