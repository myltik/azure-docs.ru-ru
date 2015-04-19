<properties
   pageTitle="Использование Hadoop Hive в HDInsight для платформы Azure"
   description="Информация об использовании Hive с HDInsight с применением протокола SSH."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Использование Hive с Hadoop в HDInsight с применением протокола SSH

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

В этой статье вы узнаете, как использовать протокол SSH для подключения к Hadoop в кластере Azure HDInsight и отправлять запросы Hive с помощью интерфейса командной строки Hive в интерактивном режиме.

> [AZURE.NOTE] Если вы уже знакомы с использованием серверов под управлением Linux Hadoop, но не знакомы с HDInsight, см. раздел <a href="../hdinsight-hadoop-linux-information/" target="_blank">Что необходимо знать о Hadoop в HDInsight на основе Linux</a>.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Hadoop в кластере HDInsight на платформе Linux.

* Клиент SSH. Клиент SSH должен входить в состав Linux, Unix и Mac OS Пользователи Windows должны загрузить клиент, например, <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Подключение с SSH

Подключитесь с помощью команды SSH, используя полное доменное имя (FQDN) кластера HDInsight. Полное доменное имя - это имя, присвоенное кластеру, с суффиксом **.azurehdinsight.net**. Например, следующая команда позволяет подключиться к кластеру с именем **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Если указан ключ сертификата для проверки подлинности SSH**, возможно, при создании кластера HDInsight потребуется указать расположение закрытого ключа в клиентской системе.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Если при создании кластера HDInsight для аутентификации SSH был задан пароль**, при появлении запроса необходимо будет ввести пароль.

###Putty (клиенты под управление Windows)

Windows не предоставляет встроенный клиент SSH. Рекомендуется использовать **Putty**, который можно загрузить по адресу: <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Дополнительные сведения об использовании Putty см. в подразделе **Использование Putty для подключения к компьютеру под управлением Linux** раздела <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Использование SSH с Linux в Azure</a>.

> [AZURE.NOTE] Если сертификат используется для проверки подлинности SSH для кластера HDInsight, также необходимо ознакомиться с подразделом **Создание PPK для Putty** раздела <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Использование SSH с Linux в Azure</a>.

##<a id="hive"></a>Использование команды Hive

2. После подключения запустите интерфейс командной строки Hive с помощью следующей команды:

        hive

3. Используя интерфейс командной строки, введите следующие инструкции, чтобы создать таблицу с именем **log4jLogs** с помощью примера данных:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Эти инструкции выполняют следующие действия.

    * **DROP TABLE** - удаляет таблицу и файл данных в случае, если таблица уже существует.
    * **CREATE EXTERNAL TABLE** - создает новую 'внешнюю' таблицу в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.
    * **ROW FORMAT** -  указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
    * **STORED AS TEXTFILE LOCATION** - указывает для Hive место, где хранятся данные (например, в каталоге example/data), а также то, что они хранятся в виде текста.
    * **SELECT** - подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Эта команда должна вернуть значение **3**, так как таблица содержит три строки с данным значением.

    > [AZURE.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные.
    >
    > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только описание таблицы.

4. Используйте следующие инструкции для создания новой 'внутренней' таблицы с именем **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Эти инструкции выполняют следующие действия.

    * **CREATE TABLE IF NOT EXISTS** - создает таблицу, если она уже не существует. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет являться "внутренней", то есть храниться в хранилище данных Hive и полностью обслуживаться Hive.
    * **STORED AS ORC** - данные будут храниться в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
    * **INSERT OVERWRITE ... SELECT** - выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.

    Чтобы убедиться в том, что только строки, столбец t4 которых содержит значение **[ERROR]**, сохранены в таблице **errorLogs**, воспользуйтесь следующей инструкцией для возврата всех строк из **errorLogs**:

        SELECT * from errorLogs;

    В результате операции должны быть возвращены три строки, у которых столбец t4 содержит значение **[ERROR]**.

    > [AZURE.NOTE] В отличие от внешних таблиц, удаление внутренних таблиц приводит также к удалению данных.

##<a id="summary"></a>Сводка

Как видите, с помощью команды Hive можно с легкостью выполнять интерактивные запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
