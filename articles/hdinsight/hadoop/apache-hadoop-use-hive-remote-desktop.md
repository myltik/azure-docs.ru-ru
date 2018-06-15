---
title: Использование Hadoop Hive и удаленного рабочего стола в HDInsight — Azure | Документы Майкрософт
description: Узнайте, как подключиться к кластеру HDInsight с помощью удаленного рабочего стола, а затем выполнить запросы Hive с помощью интерфейса командной строки Hive.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 70eab088ce87d8a62d3f258b70aaec5e2e147d0e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400645"
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Использование Hive с Hadoop в HDInsight с помощью удаленного рабочего стола
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как подключиться к кластеру HDInsight с помощью удаленного рабочего стола, а затем выполнить запросы Hive с помощью интерфейса командной строки Hive.

> [!IMPORTANT]
> Удаленный рабочий стол доступен только в кластерах HDInsight под управлением операционной системы Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> При использовании HDInsight 3.4 или более поздней версии см. сведения о выполнении запросов Hive непосредственно в кластере из командной строки: [Использование Hive с Hadoop в HDInsight с применением Beeline](apache-hadoop-use-hive-beeline.md).

## <a id="prereq"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Кластер HDInsight на платформе Windows (Hadoop в HDInsight).
* Клиентский компьютер под управлением Windows 10, Windows 8 или Windows 7.

## <a id="connect"></a>Подключение к удаленному рабочему столу
Запустите протокол удаленного рабочего стола для кластера HDInsight, а затем выполните подключение, следуя инструкциям раздела [Подключение к кластерам HDInsight с использованием RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Использование команды Hive
После подключения к рабочему столу для кластера HDInsight сделайте следующее для работы с Hive:

1. На рабочем столе HDInsight запустите **командную строку Hadoop**.
2. Введите следующую команду, чтобы запустить интерфейс командной строки Hive:

        %hive_home%\bin\hive

    После запуска интерфейса командной строки появится командная строка Hive: `hive>`.
3. С помощью этого интерфейса командной строки введите следующие операторы, чтобы создать таблицу с именем **log4jLogs** , используя пример данных:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Эти операторы выполняют следующие действия:

   * **DROP TABLE**: удаление таблицы и файла данных, если таблица уже существует.
   * **CREATE EXTERNAL TABLE**: создание новой "внешней" таблицы в Hive. Внешние таблицы хранят только определение самой таблицы в Hive, в то время как данные остаются в исходном расположении.

     > [!NOTE]
     > Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
     >
     > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.
     >
     >
   * **ROW FORMAT**: инструкции по форматированию данных для Hive. В данном случае поля всех журналов разделены пробелом.
   * **STORED AS TEXTFILE LOCATION**: информация для Hive о расположении хранения данных (каталог example/data) и о их формате (текстовый).
   * **SELECT**: подсчитывает количество строк, в которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3** , так как данное значение содержат три строки.
   * **INPUT__FILE__NAME LIKE '%.log'**  — указывает Hive, что вернуть нужно только данные из файлов с расширением LOG. Это ограничивает поиск файлом sample.log, в котором содержатся данные, и предотвращает возврат данных из других примеров файлов данных, не соответствующих определенной нами схеме.
4. Используйте следующие операторы, чтобы создать новую "внутреннюю" таблицу с именем **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Эти операторы выполняют следующие действия:

   * **CREATE TABLE IF NOT EXISTS**: создание таблицы, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не использовалось, такая таблица будет внутренней, то есть хранящейся в хранилище данных Hive и полностью управляемой Hive.

     > [!NOTE]
     > В отличие от **внешних** таблиц, удаление внутренней таблицы приводит к удалению базовых данных.
     >
     >
   * **STORED AS ORC**: хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
   * **INSERT OVERWRITE ... SELECT**: выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.

     Чтобы убедиться, что в таблице **errorLogs** сохранены только строки, столбец t4 которых содержит значение **[ERROR]**, воспользуйтесь следующим оператором, чтобы отобразить все строки из таблицы **errorLogs**:

       SELECT * from errorLogs;

     В результате операции должны быть возвращены три строки, в которых столбец t4 содержит значение **[ERROR]** .

## <a id="summary"></a>Сводка
Как видите, с помощью команды Hive можно с легкостью выполнять интерактивные запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a id="nextsteps"></a>Дальнейшие действия
Общая информация о Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Если вы используете Tez с Hive, обратитесь к следующим документам для сведений об отладке:

* [Использование пользовательского интерфейса Tez в HDInsight на платформе Windows](../hdinsight-debug-tez-ui.md)
* [Использование представления Ambari Tez в HDInsight на платформе Linux](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
