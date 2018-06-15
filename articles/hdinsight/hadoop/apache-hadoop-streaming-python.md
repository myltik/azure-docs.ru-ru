---
title: Разработка заданий потоковой передачи Python MapReduce в HDInsight — Azure | Документы Майкрософт
description: Узнайте, как использовать Python для заданий потоковой передачи MapReduce. Hadoop предоставляет интерфейс API потоковой передачи для MapReduce с целью написания заданий на языках, отличных от Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: b5e19f81c3e869347f21ab3c70a70016196b946d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400520"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Разработка программ MapReduce с потоковой передачей Python для HDInsight

Узнайте, как использовать Python для операций потоковой передачи MapReduce. Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. Действия, описанные в этом документе, предназначены для реализации компонентов Map и Reduce на языке Python.

## <a name="prerequisites"></a>предварительным требованиям

* Hadoop в кластере HDInsight на платформе Linux

  > [!IMPORTANT]
  > Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Текстовый редактор

  > [!IMPORTANT]
  > Текстовый редактор должен использовать LF в качестве конца строки. Использование конца строки CRLF приведет к ошибкам при выполнении задания MapReduce в кластерах HDInsight под управлением Linux.

* Команды `ssh` и `scp` или [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Статистика

В этом примере реализуется простая функция подсчета слов, реализованная в модулях сопоставления и сжатия в Python. Модуль сопоставления делит предложение на отдельные слова, а модуль сжатия объединяет слова и подсчитывает их количество для вывода выходных данных, т. е. результата.

На следующей блок-схеме показано, что происходит на этапах сопоставления и сжатия.

![Иллюстрация процесса MapReduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Потоковая передача MapReduce

Hadoop позволяет указать файл, содержащий логику сопоставления и сжатия, используемую для выполнения задания. К сопоставлению и сжатию выдвигаются определенные требования.

* **Входные данные**— компоненты сопоставления и сжатия должны считывать входные данные с STDIN.
* **Выходные данные**— компоненты сопоставления и сжатия должны записывать выходные данные в STDOUT.
* **Формат данных**— полученные и созданные данные должны быть парой «ключ-значение», разделенной символом табуляции.

Python позволяет с легкостью выполнить эти требования, используя модуль `sys` для чтения с STDIN и `print` для вывода в STDOUT. Остается просто отформатировать данные, вставив символ табуляции (`\t`) между ключом и значением.

## <a name="create-the-mapper-and-reducer"></a>Создание средств сопоставления и приведения

1. Создайте файл с именем `mapper.py` и в качестве его содержимого используйте следующий код:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Создайте файл с именем **reducer.py** и в качестве его содержимого используйте следующий код:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Запуск с помощью PowerShell

Чтобы файлы имели правильные концы строк, используйте следующий скрипт PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Чтобы отправить файлы, запустить задание и просмотреть выходные данные, используйте следующий скрипт PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Запуск из сеанса SSH

1. В среде разработки в том же каталоге, в котором расположены файлы `mapper.py` и `reducer.py`, выполните следующую команду:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Замените `username` именем пользователя SSH для кластера, а `clustername` — именем кластера.

    Эта команда копирует файлы из локальной системы на головной узел.

    > [!NOTE]
    > Если для защиты учетной записи SSH использовался пароль, будет предложено его ввести. Если использовался ключ SSH, возможно, нужно будет использовать параметр `-i` и указать путь к закрытому ключу. Например, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Подключитесь к кластеру с помощью SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Чтобы mapper.py и reducer.py имели правильные концы строк, используйте следующие команды:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Используйте следующую команду для запуска задания MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Эта команда состоит из следующих частей:

   * **hadoop streaming.jar**— используется при выполнении операций потоковой передачи MapReduce. Эта часть обеспечивает взаимодействие Hadoop с предоставленным внешним кодом MapReduce.

   * **-files** — добавляет указанные файлы в задание MapReduce.

   * **-mapper**— сообщает Hadoop о том, какой файл следует использовать в качестве модуля сопоставления.

   * **-reducer**— сообщает Hadoop о том, какой файл следует использовать в качестве модуля сжатия.

   * **-input**— входной файл, в котором нужно подсчитать слова.

   * **-output** — каталог, в который записываются выходные данные.

    Процесс выполнения задания MapReduce отображается в виде процентов.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. Чтобы просмотреть выходные данные, используйте следующую команду:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Команда отобразит список слов и частоту их появления.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как использовать задания потоковой передачи MapReduce в HDInsight, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)
