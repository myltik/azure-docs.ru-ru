<properties
pageTitle="Использование DataFu с Pig в HDInsight"
description="DataFu — это коллекция библиотек, предназначенных для использования с Hadoop. Узнайте, как использовать DataFu с Pig в кластере HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="03/18/2016"
ms.author="larryfr"/>

#Использование DataFu с Pig в HDInsight

DataFu — это коллекция библиотек с открытым исходным кодом, предназначенных для использования с Hadoop. В этом документе вы узнаете, как использовать DataFu в кластере HDInsight и как применять пользовательские функции (UDF) DataFu с Pig.

##Предварительные требования

* Подписка Azure.

* Кластер Azure HDInsight (на платформе Linux или Windows)

* Краткое знакомство с [использованием Pig в HDInsight](hdinsight-use-pig.md)

##Установка DataFu в HDInsight под управлением Linux

> [AZURE.NOTE] DataFu предустанавливается в кластерах HDInsight под управлением Windows. Если вы используете кластер под управлением Windows, пропустите этот раздел.

DataFu можно скачать и установить из репозитория Maven. Чтобы добавить DataFu в свой кластер HDInsight, выполните следующие действия:

1. Подключитесь к кластеру HDInsight под управлением Linux через SSH. Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

    * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, OS X или Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Используйте следующую команду, чтобы скачать JAR-файл DataFu с помощью служебной программы wget, либо скопируйте и вставьте ссылку в браузере, чтобы начать скачивание.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Затем загрузите файл в хранилище по умолчанию для кластера HDInsight. В результате файл становится доступным для всех узлов в кластере и остается в хранилище даже в случае удаления и повторного создания кластера.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] Приведенный выше пример сохраняет JAR-файл в `wasb:///example/jars`, так как этот каталог уже существует в хранилище кластера. Можно использовать любое расположение в хранилище кластера HDInsight.

##Использование DataFu с Pig

В описанных здесь действиях предполагается, что вы знакомы с использованием Pig в HDInsight, и приводятся только операторы Pig Latin без описания их использования с кластером. Дополнительные сведения об использовании Pig с HDInsight см. в статье [Использование Pig с HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] При использовании DataFu из Pig в кластере HDInsight под управлением Linux необходимо сначала зарегистрировать JAR-файл с помощью следующего оператора Pig Latin:
>
> ```register wasb:///example/jars/datafu-1.2.0.jar```
>
> В кластерах HDInsight под управлением Windows DataFu регистрируется по умолчанию.

Обычно для функций DataFu определяется псевдоним. Например:

    DEFINE SHA datafu.pig.hash.SHA();
    
Здесь определяется псевдоним с именем `SHA` для функции хэширования SHA. Затем его можно использовать в скрипте Pig Latin в целях создания хэша для входных данных. Например, следующий код заменяет имена во входных данных на хэш-значение:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Если используются следующие входные данные:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Получается следующий результат:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##Дальнейшие действия

Дополнительные сведения о DataFu или Pig см. в следующих документах:

* [Руководство по Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Использование Pig с HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0323_2016-->