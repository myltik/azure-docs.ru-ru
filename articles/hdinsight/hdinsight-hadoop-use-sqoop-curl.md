---
title: "Использование Hadoop Sqoop с Curl в HDInsight | Документация Майкрософт"
description: "Узнайте об удаленной отправке заданий Sqoop в HDInsight с помощью Curl."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6c468c8ed0d817a32c343d55cac780b904ee8668
ms.openlocfilehash: 02674646a0dac8a468b8c4f9f079630103058a3a
ms.lasthandoff: 02/07/2017


---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Выполнение заданий Sqoop с Hadoop в HDInsight с помощью Curl
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как с помощью Curl выполнять задания Sqoop в кластере Hadoop в HDInsight.

Curl используется для демонстрации возможностей взаимодействия с HDInsight с помощью необработанных HTTP-запросов для выполнения и мониторинга заданий Sqoop, а также получения их результатов. Для этого используется REST API для WebHCat (прежнее название — Templeton), предоставляемый кластером HDInsight.

> [!NOTE]
> Если вы уже знаете, как использовать серверы Hadoop на платформе Linux, но не знакомы с HDInsight, ознакомьтесь со статьей [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Hadoop в кластере HDInsight (на платформе Linux или Windows)
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a name="submit-sqoop-jobs-by-using-curl"></a>Отправка заданий Sqoop с помощью Curl
> [!NOTE]
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
> В командах, описанных в этом разделе, замените **USERNAME** на имя пользователя для выполнения проверки подлинности в кластере, а **PASSWORD** — на пароль учетной записи пользователя. Замените **CLUSTERNAME** именем кластера.
> 
> REST API защищен с помощью [обычной проверки подлинности](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.
> 
> 

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Вы должны получить ответ, аналогичный показанному ниже:
   
        {"status":"ok","version":"v1"}
   
    Ниже приведены параметры, используемые в этой команде:
   
   * **-u** — имя пользователя и пароль, используемый для аутентификации запроса.
   * **-G** — указывает, что это запрос GET.
     
     Начало URL-адреса **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** будет одинаковым для всех запросов. Путь **/status** указывает, что по запросу серверу должно быть возвращено состояние WebHCat (другое название — Templeton). 
2. Чтобы отправить задание Sqoop, воспользуйтесь следующей командой:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Ниже приведены параметры, используемые в этой команде:

    * **-d** — так как `-G` не используется, в запросе по умолчанию используется метод POST. `-d` задает значения данных, отправляемые в запросе.

        * **user.name** — пользователь, выполняющий команду.

        * **command** — выполняемая команда Sqoop.

        * **statusdir** — каталог, в который будет записано состояние этого задания.

    Эта команда должна возвращать идентификатор задания, который может использоваться для проверки состояния задания.

        {"id":"job_1415651640909_0026"}

1. Чтобы проверить состояние задания, используйте следующую команду. Замените **JOBID** значением, возвращенным на предыдущем шаге. Например, если возвращено значение `{"id":"job_1415651640909_0026"}`, то **JOBID** будет `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Если задание завершено, у него будет состояние **SUCCEEDED**(Успешно).
   
   > [!NOTE]
   > Этот запрос Curl возвращает документ нотации объектов JavaScript с информацией о задании. При этом jq используется только для получения значения состояния.
   > 
   > 
2. После изменения состояния задания на **SUCCEEDED** результаты задания можно получить из хранилища больших двоичных объектов Azure. Параметр `statusdir`, передаваемый с помощью запроса, содержит расположение выходного файла. В данном случае это **wasbs:///example/curl**. При использовании этого адреса выходные данные задания сохраняются в каталоге **example/curl** в контейнере хранилища, используемом по умолчанию кластером HDInsight.
   
    Вы можете вывести список этих файлов и скачать их с помощью [интерфейса командной строки Azure](../xplat-cli-install.md). Например, для просмотра списка файлов в **example/curl**можно использовать следующую команду:
   
        azure storage blob list <container-name> example/curl
   
    Чтобы скачать файл:
   
        azure storage blob download <container-name> <blob-name> <destination-file>
   
   > [!NOTE]
   > Необходимо либо указать имя учетной записи хранения, содержащей большой двоичный объект, с помощью параметров `-a` и `-k`, либо задать переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**. См. также: <a href="hdinsight-upload-data.md" target="_blank".
   > 
   > 

## <a name="limitations"></a>Ограничения
* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется переключатель `-batch` , Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="summary"></a>Сводка
Как показано в этом документе, для запуска, мониторинга и просмотра результатов выполнения заданий Sqoop в кластере HDInsight можно использовать необработанные HTTP-запросы.

Дополнительную информацию об интерфейсе REST, используемом в этой статье, см. в <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">справочнике по Sqoop REST API</a>.

## <a name="next-steps"></a>Дальнейшие действия
Общая информация об использовании Hive в HDInsight:

* [Использование Sqoop с Hadoop в HDInsight (Windows)](hdinsight-use-sqoop.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
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


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx



