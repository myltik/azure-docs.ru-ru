---
title: "Использование MapReduce и подключения SSH с Hadoop в HDInsight | Документация Майкрософт"
description: "Информация об использовании SSH для выполнения заданий MapReduce с помощью Hadoop в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: fbf33ea6a6362857bf4bc92055cabd9b099a6d0c
ms.lasthandoff: 04/12/2017


---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Использование MapReduce с Hadoop в HDInsight с помощью SSH

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этой статье вы узнаете, как подключиться к Hadoop в кластере HDInsight по протоколу SSH и отправить задания MapReduce с помощью команд Hadoop, используя этот протокол.

> [!NOTE]
> Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, см. статью [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Кластер HDInsight на основе Linux (Hadoop в HDInsight).

  > [!IMPORTANT]
  > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Клиент SSH. Клиент SSH должен входить в состав операционных систем Linux, Unix и Mac OS. Пользователи Windows должны загрузить отдельный клиент, например [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a id="ssh"></a>Подключение по SSH

Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя — это имя, присвоенное кластеру, заканчивающееся на **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для аутентификации SSH**, возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе. Пример:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

Если при создании кластера HDInsight **для аутентификации SSH был задан пароль**, при появлении запроса необходимо будет ввести пароль.

Дополнительные сведения об использовании протокола SSH с HDInsight см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Использование команд Hadoop

1. После подключения к кластеру HDInsight используйте следующую команду **Hadoop** , чтобы запустить задание MapReduce:

    ```
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    При этом запустится класс **wordcount**, содержащийся в файле **hadoop-mapreduce-examples.jar**. В качестве входных данных он использует документ **/example/data/gutenberg/davinci.txt**, а выходные данные сохраняются в **/example/data/WordCountOutput**.

    > [!NOTE]
    > Дополнительную информацию об этом задании MapReduce и примере данных см. в разделе [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md).

2. Задание выдает информацию о ходе обработки, а по завершении задания возвращает информацию, аналогичную приведенной ниже:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. После завершения используйте следующую команду, чтобы вывести список выходных файлов, сохраненных в **wasb://example/data/WordCountOutput**.

    ```
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Должно отобразиться два файла: **_SUCCESS** и **part-r-00000**. Файл **part-r-00000** содержит выходные данные этого задания.

    > [!NOTE]
    > Некоторые задания MapReduce могут разделять результаты на несколько файлов **part-r-№№№№№** . В этом случае используйте суффикс №№№№№, чтобы определить порядок файлов.

4. Чтобы просмотреть выходные данные, используйте следующую команду:

    ```
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Она отобразит список слов, содержащихся в файле **wasb://example/data/gutenberg/davinci.txt**, а также количество вхождений каждого из них. Ниже приведен пример данных, содержащихся в файле.

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Сводка

Как видите, команды Hadoop позволяют с легкостью выполнять задания MapReduce в кластере HDInsight и просматривать выходные данные задания.

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

