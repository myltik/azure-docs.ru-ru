---
title: Использование MapReduce и подключения SSH с Hadoop в HDInsight — Azure | Документы Майкрософт
description: Информация об использовании SSH для выполнения заданий MapReduce с помощью Hadoop в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlunb
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 67e1bf6cee04eda51f5dbfc51a95614347fc2b7f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31399010"
---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Использование MapReduce с Hadoop в HDInsight с помощью SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Узнайте, как отправлять задания MapReduce в HDInsight с помощью подключения SSH.

> [!NOTE]
> Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, см. статью [Сведения об использовании HDInsight в Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Предварительные требования

* Кластер HDInsight на основе Linux (Hadoop в HDInsight).

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Клиент SSH. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="ssh"></a>Подключение по SSH

Подключитесь к кластеру с помощью SSH. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight** с учетной записью **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Если вы используете ключ сертификата для аутентификации SSH**, возможно, потребуется указать расположение закрытого ключа в клиентской системе. Пример:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Если вы используете пароль для аутентификации SSH**, его необходимо ввести при запросе.

Дополнительные сведения об использовании протокола SSH с HDInsight см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Использование команд Hadoop

1. После подключения к кластеру HDInsight используйте следующую команду, чтобы запустить задание MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Эта команда запускает класс `wordcount`, который содержится в файле `hadoop-mapreduce-examples.jar`. Она использует документ `/example/data/gutenberg/davinci.txt` в качестве входных данных, а выходные данные хранятся в `/example/data/WordCountOutput`.

    > [!NOTE]
    > Дополнительную информацию об этом задании MapReduce и примере данных см. в разделе [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md).

2. Задание выдает информацию о ходе обработки, а по завершении задания возвращает информацию, аналогичную приведенной ниже:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. По завершении задания воспользуйтесь следующей командой, чтобы просмотреть выходные файлы:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Эта команда отображает два файла — `_SUCCESS` и `part-r-00000`. Файл `part-r-00000` содержит выходные данные этого задания.

    > [!NOTE]
    > Некоторые задания MapReduce могут разделять результаты на несколько файлов **part-r-№№№№№** . В этом случае используйте суффикс №№№№№, чтобы определить порядок файлов.

4. Чтобы просмотреть выходные данные, используйте следующую команду:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Она отобразит список слов, содержащихся в файле **wasb://example/data/gutenberg/davinci.txt**, а также число вхождений каждого из них. Ниже приведен пример данных, содержащихся в файле.

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
