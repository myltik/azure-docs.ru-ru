---
title: "Анализ данных Twitter с помощью Apache Hive в HDInsight | Документация Майкрософт"
description: "Узнайте, как использовать Python для хранения твитов, содержащих определенные ключевые слова, а затем воспользоваться Hive и Hadoop в HDInsight для преобразования необработанных данных Twitter в поддерживающую поиск таблицу Hive."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 660524509b523ae2edb72cb8c80d75437730c119


---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Анализ данных Twitter с помощью Hive в HDInsight
В данном документе рассказывается, как получать твиты с использованием API потоковой передачи Twitter, а затем использовать Apache Hive в кластере HDInsight под управлением Linux для обработки данных в формате JSON. Результатом будет список пользователей Twitter, отправивших большинство твитов, которые содержат определенное слово.

> [!NOTE]
> Хотя отдельные части этого документа можно использовать для кластеров HDInsight под управлением Windows (например, Python), многие действия относятся к кластерам HDInsight под управлением Linux. Описание действий для кластеров на основе Windows см. в статье [Анализ данных Twitter с помощью Hive в HDInsight](hdinsight-analyze-twitter-data.md).
> 
> 

### <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Кластер Azure HDInsight на основе Linux**. Информацию о создании кластера см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md), содержащей указания по созданию кластера.
* **Клиент SSH**. Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:
  
  * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
  * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* **Python** и [pip](https://pypi.python.org/pypi/pip)

## <a name="get-a-twitter-feed"></a>Получение веб-канала Twitter
Twitter позволяет получать [данные для каждого твита](https://dev.twitter.com/docs/platform-objects/tweets) в виде документа JavaScript Object Notation (JSON) с помощью интерфейса REST API. [OAuth](http://oauth.net) . Необходимо также создать *приложение Twitter* , содержащее параметры, используемые для доступа к API.

### <a name="create-a-twitter-application"></a>Создание приложения Twitter
1. С помощью браузера войдите на веб-сайт [https://apps.twitter.com/](https://apps.twitter.com/). Щелкните ссылку **Войти сейчас** , если у вас нет учетной записи Twitter.
2. Щелкните **Создать новое приложение**.
3. Введите **Имя**, **Описание**, **Веб-сайт**. В поле **Веб-сайт** можно использовать URL-адрес. В следующей таблице приведены некоторые примеры значений:
   
   | Поле | Значение |
   |:--- |:--- |
   | Имя |MyHDInsightApp |
   | Описание |MyHDInsightApp |
   | Веб-сайт |http://www.myhdinsightapp.com |
4. Установите флажок **Я принимаю** и нажмите кнопку **Создать приложение Twitter**.
5. Выберите вкладку **Разрешения** . Разрешение по умолчанию: **Только для чтения**. Этого разрешения достаточно для данного учебника.
6. Перейдите на вкладку **Ключи и токены доступа** .
7. Нажмите кнопку **Создать маркер доступа**.
8. Нажмите кнопку **Проверить OAuth** в правом верхнем углу страницы.
9. Запишите **ключ клиента**, **Секрет клиента**, **Маркер доступа** и **Секрет маркера доступа**. Эти значения потребуются позже.

> [!NOTE]
> При использовании команды Curl в Windows указывайте значения параметров в двойных кавычках вместо одинарных.
> 
> 

### <a name="download-tweets"></a>Скачивание твитов
Следующий код Python скачает 10 000 твитов из Twitter и сохранит их в файл с именем **tweets.txt**.

> [!NOTE]
> Так как Python уже установлен, в кластере HDInsight выполняются следующие действия.
> 
> 

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Если для защиты учетной записи SSH используется пароль, будет предложено ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i` , чтобы указать соответствующий закрытый ключ. Например, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:
   
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. По умолчанию на головном узле HDInsight не установлена служебная программа **pip** . Используйте следующую команду для ее установки, а затем обновите эту служебную программу:
   
        sudo apt-get install python-pip
        sudo pip install --upgrade pip
3. Код для скачивания твитов основан на [Tweepy](http://www.tweepy.org/) и [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Используйте следующую команду для их установки:
   
        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
   
   > [!NOTE]
   > Биты удаления openssl-python, установки python-dev, libffi-dev, libssl-dev, pyOpenSSL и requests[security] позволяют избежать предупреждения InsecurePlatform при подключении к Twitter через SSL из Python.
   > 
   > Tweepy v3.2.0 используется для предотвращения [ошибки](https://github.com/tweepy/tweepy/issues/576) , которая может произойти при обработке твитов.
   > 
   > 
4. Создайте новый файл **gettweets.py**следующей командой:
   
        nano gettweets.py
5. Добавьте в файл **gettweets.py** следующее содержимое. Вместо заполнителей **consumer\_secret**, **consumer\_key**, **access/\_token**, and **access\_token\_secret** введите данные из своего приложения Twitter.
   
        #!/usr/bin/python
   
        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys
   
        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'
   
        #The number of tweets we want to get
        max_tweets=10000
   
        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()
   
            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True
   
            #Handle any errors that may occur
            def on_error(self, status):
                print status
   
        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])
6. Нажмите клавиши **Ctrl + X**, а затем **Y** (Да) для сохранения файла.
7. Чтобы запустить файл и скачать твиты, выполните следующую команду:
   
        python gettweets.py
   
    Должен появиться индикатор выполнения, который дойдет до 100 % по мере скачивания и сохранения твитов в файл.
   
   > [!NOTE]
   > Если индикатор хода выполнения перемещается очень долго, следует изменить фильтр, чтобы отслеживать популярные темы. При наличии множества доступных твитов по отфильтровываемой теме вы сможете быстро получить 10 000 необходимых записей.
   > 
   > 

### <a name="upload-the-data"></a>Передача данных
Чтобы передать данные в WASB (распределенную файловую систему, используемую HDInsight), используйте следующие команды:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Это сохранит данные в расположении, к которому могут обращаться все узлы в кластере.

## <a name="run-the-hiveql-job"></a>Выполнение задания HiveQL
1. Используйте следующую команду, чтобы создать файл, содержащий инструкции HiveQL:
   
        nano twitter.hql
   
    Добавьте в этот файл следующее содержимое:
   
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response,    '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
2. Нажмите клавиши **Ctrl + X**, а затем **Y** (Да) для сохранения файла.
3. Выполните приведенную ниже команду, чтобы запустить код HiveQL в файле:
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
   
    Она загрузит оболочку Hive, запустит код HiveQL из файла **twitter.hql**, а затем вернет командную строку `jdbc:hive2//localhost:10001/>`.
4. В командной строке Beeline выполните следующий код, чтобы убедиться, что вы можете выбрать данные из таблицы **tweets**, созданной HiveQL из файла **twitter.hql**:
   
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
   
    Будет возвращено не более 10 твитов, содержащих слово **Azure** в тексте сообщения.

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике мы рассмотрели, как преобразовать неструктурированный набор данных JSON в структурированную таблицу Hive для запроса, исследования и анализа данных из Twitter с помощью HDInsight в Azure. Дополнительные сведения см. на следующих ресурсах:

* [Приступая к работе с HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Анализ данных о задержке рейсов с помощью HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter



<!--HONumber=Nov16_HO3-->


