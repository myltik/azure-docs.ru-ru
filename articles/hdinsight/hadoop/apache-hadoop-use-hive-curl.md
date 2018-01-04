---
title: "Использование Hadoop Hive с cURL в HDInsight — Azure | Документы Майкрософт"
description: "Информация об удаленной отправке заданий Pig в HDInsight с помощью Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: b05dbdcec3cfb5c78115061567bb6229623cd0ff
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Выполнение запросов Hive с Hadoop в HDInsight с помощью REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Узнайте, как использовать REST API WebHCat для выполнения запросов Hive с Hadoop в кластере Azure HDInsight.

[Curl](http://curl.haxx.se/) используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов. Служебная программа [Jq](http://stedolan.github.io/jq/) используется для обработки данных JSON, возвращаемых запросами REST.

> [!NOTE]
> Если вы уже знакомы с использованием серверов под управлением Linux Hadoop, но не знакомы с HDInsight, ознакомьтесь с документом [Что необходимо знать о Hadoop в HDInsight на основе Linux](../hdinsight-hadoop-linux-information.md).

## <a id="curl"></a>Выполнение запросов Hive

> [!NOTE]
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight.
>
> В командах в этом разделе замените **admin** именем пользователя, используемым для аутентификации в кластере. Замените **CLUSTERNAME** именем кластера. При появлении запроса введите пароль для учетной записи пользователя.
>
> REST API защищен с помощью [обычной проверки подлинности](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Вы должны получить ответ, аналогичный показанному ниже тексту.

        {"status":"ok","version":"v1"}

    Ниже приведены параметры, используемые в этой команде:

    * **-u** — имя пользователя и пароль, используемый для аутентификации запроса.
    * **-G** — указывает, что это запрос, являющийся операцией GET.

   Начало URL-адреса **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** одинаковое для всех запросов. Путь **/status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название — Templeton). Используя следующую команду, можно также запросить версию Hive:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Этот запрос возвращает ответ, аналогичный показанному ниже тексту.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Используйте следующую команду, чтобы создать таблицу **log4jLogs**.

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Ниже приведены параметры, используемые в запросе.

   * **-d** — так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.

     * **user.name** — пользователь, выполняющий команду.
     * **execute** — операторы HiveQL, которые необходимо выполнить.
     * **statusdir** — каталог, в который будет записано состояние этого задания.

   Эти операторы выполняют следующие действия:
   
   * **DROP TABLE** — если таблица уже существует, она будет удалена.
   * **CREATE EXTERNAL TABLE** : создание новой "внешней" таблицы в Hive. Внешние таблицы хранят только определение таблицы в Hive. Данные остаются в исходном расположении.

     > [!NOTE]
     > Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, процессом автоматизированной передачи данных или другой операцией MapReduce.
     >
     > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

   * **ROW FORMAT** — настройка форматирования данных. Поля всех журналов разделены пробелами.
   * **STORED AS TEXTFILE LOCATION** — указание расположения хранения данных (каталог example/data) и их формата (текст).
   * **SELECT** : подсчитывает количество строк, у которых столбец **t4** содержит значение **[ERROR]**. Эта инструкция возвращает значение **3**, так как данное значение содержат три строки.

     > [!NOTE]
     > Обратите внимание, что при использовании Curl пробелы между операторами HiveQL заменяются знаком `+`. Заключенные в кавычки значения, содержащие пробелы в качестве разделителя, заменять на `+`не нужно.

   * **INPUT__FILE__NAME LIKE '%25.log'** — поиск будет ограничен только файлами с расширением LOG.

     > [!NOTE]
     > Обратите внимание, что `%25` — это % в кодировке URL-адреса, поэтому фактическим условием является `like '%.log'`. Символ % должен быть в кодировке URL, поскольку в URL-адресах он рассматривается как специальный символ.

   Эта команда возвращает идентификатор задания, который может использоваться для проверки состояния задания.

       {"id":"job_1415651640909_0026"}

3. Чтобы проверить состояние задания, используйте следующую команду.

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то **JOBID** будет `job_1415651640909_0026`.

    Если задание завершено, оно будет в состоянии **SUCCEEDED** (Успешно).

   > [!NOTE]
   > Этот запрос Curl возвращает документ нотации объектов JavaScript с информацией о задании. При этом jq используется только для получения значения состояния.

4. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это **/example/curl**. Этот адрес задает каталог **/example/curl** для сохранения выходных данных, который размещен в хранилище по умолчанию для кластера.

    Вы можете вывести список этих файлов и скачать их с помощью [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Дополнительные сведения об использовании Azure CLI со службой хранилища Azure см. в документе [Использование Azure CLI 2.0 со службой хранилища Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

5. Используйте следующие операторы, чтобы создать новую "внутреннюю" таблицу с именем **errorLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Эти операторы выполняют следующие действия:

   * **CREATE TABLE IF NOT EXISTS** : создание таблицы, если она до этого не существовала. Эта инструкция создает внутреннюю таблицу, которая хранится в хранилище данных Hive. Она полностью обслуживается Hive.

     > [!NOTE]
     > В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

   * **STORED AS ORC** : хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
   * **INSERT OVERWRITE ... SELECT**: выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.
   * **SELECT** — выбираются все строки из новой таблицы **errorLogs**.

6. Используйте идентификатор задания, возвращаемый для проверки состояния задания. После его успешного выполнения используйте Azure CLI, как было описано ранее, чтобы скачать и просмотреть результаты. Выходные данные должны содержать три строки, в каждой из которых должен быть текст **[ERROR]**.

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация об использовании Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Если вы используете Tez с Hive, обратитесь к следующим документам для сведений об отладке:

* [Использование представления Ambari Tez в HDInsight на платформе Linux](../hdinsight-debug-ambari-tez-view.md)

Дополнительную информацию о REST API, используемом в этом документе, см. в [справочнике по WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

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




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


