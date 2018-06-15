---
title: Использование DataFu с Pig в HDInsight — Azure | Документы Майкрософт
description: DataFu — это коллекция библиотек, предназначенных для использования с Hadoop. Узнайте, как использовать DataFu с Pig в кластере HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 30243d0b7db41fbe19c60d6c11d56fb7e801797b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401012"
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Использование DataFu с Pig в HDInsight

Узнайте, как использовать DataFu с HDInsight. DataFu — это коллекция библиотек с открытым исходным кодом, предназначенных для использования с Pig в Hadoop.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure.

* Кластер Azure HDInsight (на платформе Linux или Windows)

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Краткое знакомство с [использованием Pig в HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Установка DataFu в HDInsight под управлением Linux

> [!IMPORTANT]
> DataFu устанавливается на кластеры под управлением Linux версии 3.3 и более поздней и кластеры под управлением Windows. Эту коллекцию нельзя установить на кластеры под управлением Linux версии ниже 3.3.
>
> Если вы используете кластер под управлением Linux версии 3.3 или более поздней либо кластер под управлением Windows, этот раздел можно пропустить.

DataFu можно скачать и установить из репозитория Maven. Чтобы добавить DataFu в свой кластер HDInsight, выполните следующие действия:

1. Подключитесь к кластеру HDInsight под управлением Linux через SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

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
> Если вы установили DataFu вручную, следуя приведенными в предыдущем разделе инструкциям, перед использованием необходимо выполнить регистрацию.
>
> * Если ваш кластер использует службу хранилища Azure, используйте путь `wasb://`. Например, `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> * Если ваш кластер использует Azure Data Lake Store, используйте путь `adl://`. Например, `register adl://home/example/jars/datafu-1.2.0.jar`.

Зачастую для функций DataFu определяется псевдоним. В следующем примере определяется псевдоним `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Затем псевдоним можно использовать в скрипте Pig Latin в целях создания хэша для входных данных. Например, следующий код заменяет расположение во входных данных на хэш-значение:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Это дает следующий результат:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о DataFu или Pig см. в следующих документах:

* [Руководство по Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
