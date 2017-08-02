---
title: "Использование DataFu с Pig в HDInsight — Azure | Документы Майкрософт"
description: "DataFu — это коллекция библиотек, предназначенных для использования с Hadoop. Узнайте, как использовать DataFu с Pig в кластере HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 272159d5017e62d1f606e69d6bdcde8ecbc0e3bf
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017


---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Использование DataFu с Pig в HDInsight

Узнайте, как использовать DataFu с HDInsight. DataFu — это коллекция библиотек с открытым исходным кодом, предназначенных для использования с Pig в Hadoop.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure.

* Кластер Azure HDInsight (на платформе Linux или Windows)

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Краткое знакомство с [использованием Pig в HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Установка DataFu в HDInsight под управлением Linux

> [!NOTE]
> DataFu устанавливается на кластеры под управлением Linux версии 3.3 и более поздней и кластеры под управлением Windows. Эту коллекцию нельзя установить на кластеры под управлением Linux версии ниже 3.3.
>
> Если вы используете кластер под управлением Linux версии 3.3 или более поздней или кластер под управлением Windows, этот раздел можно пропустить.

DataFu можно скачать и установить из репозитория Maven. Чтобы добавить DataFu в свой кластер HDInsight, выполните следующие действия:

1. Подключитесь к кластеру HDInsight под управлением Linux через SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Используйте следующую команду, чтобы скачать JAR-файл DataFu с помощью служебной программы wget, либо скопируйте и вставьте ссылку в браузере, чтобы начать скачивание.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. Затем загрузите файл в хранилище по умолчанию для кластера HDInsight. Размещение файла в хранилище по умолчанию хранилища делает его доступным для всех узлов в кластере.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > Предыдущая команда сохраняет JAR-файл в `/example/jars`, так как этот каталог уже существует в хранилище кластера. Можно использовать любое расположение в хранилище кластера HDInsight.

## <a name="use-datafu-with-pig"></a>Использование DataFu с Pig

При выполнении шагов в этом разделе предполагается, что вы знакомы с использованием Pig в HDInsight. Дополнительные сведения об использовании Pig с HDInsight см. в статье [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> При использовании DataFu из Pig в кластере HDInsight под управлением Linux необходимо сначала зарегистрировать JAR-файл.
>
> Если ваш кластер использует службу хранилища Azure, используйте путь `wasb://`. Например, `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> Если ваш кластер использует Azure Data Lake Store, используйте путь `adl://`. Например, `register adl://home/example/jars/datafu-1.2.0.jar`.
>
> В кластерах HDInsight под управлением Windows DataFu регистрируется по умолчанию.

Зачастую для функций DataFu определяется псевдоним. Например:

    DEFINE SHA datafu.pig.hash.SHA();

Эта инструкция определяет псевдоним с именем `SHA` для функции хэширования SHA. Затем псевдоним можно использовать в скрипте Pig Latin в целях создания хэша для входных данных. Например, следующий код заменяет имена во входных данных на хэш-значение:

```piglatin
raw = LOAD '/data/raw/' USING PigStorage(',') AS  
    (name:chararray,
    int1:int,
    int2:int,
    int3:int);
mask = FOREACH raw GENERATE SHA(name), int1, int2, int3;
DUMP mask;
```

Если этот код используется со следующими входными данными:

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

Это дает следующий результат:

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о DataFu или Pig см. в следующих документах:

* [Руководство по Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Использование Pig с HDInsight](hdinsight-use-pig.md)

