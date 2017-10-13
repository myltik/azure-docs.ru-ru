---
title: "Apache Sqoop с Hadoop в Azure HDInsight | Документация Майкрософт"
description: "Сведения об использовании Apache Sqoop для импорта и экспорта между Hadoop в HDInsight и базой данных SQL Azure."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: larryfr
ms.openlocfilehash: 35dcbb91e6af1480685c9fd5b829c54277c1c605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Использование Apache Sqoop для импорта и экспорта между Hadoop в HDInsight и базой данных SQL

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop для импорта и экспорта между кластером Hadoop в Azure HDInsight и базой данных SQL Azure или базой данных Microsoft SQL Server. В этом руководстве используется команда `sqoop` ​​непосредственно из головного узла кластера Hadoop. В этом документе вы подключитесь к головному узлу, используя SSH, и выполните команды.

> [!IMPORTANT]
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="install-freetds"></a>Установка FreeTDS

1. Подключитесь к кластеру HDInsight с помощью SSH. Например, следующая команда подключается к основному головному узлу кластера с именем `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Используйте следующую команду для установки FreeTDS:

    ```bash
    sudo apt --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS будет использоваться в нескольких действиях для подключения к базе данных SQL.

## <a name="create-the-table-in-sql-database"></a>Создание таблицы в базе данных SQL

> [!IMPORTANT]
> Если вы используете кластер HDInsight и базу данных SQL, созданные при изучении руководства [Использование Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop.md), пропустите действия, описанные в этом разделе. База данных и таблица были созданы при выполнении действий, описанных в документе [Использование Sqoop с Hadoop в HDInsight (SSH)](hdinsight-use-sqoop.md).

1. В сеансе SSH используйте следующую команду для подключения к серверу базы данных SQL.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Должен появиться результат, аналогичный приведенному ниже тексту.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. В командной строке `1>` введите следующий запрос.

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    Если вводится инструкция `GO`, то оцениваются предыдущие инструкции. Сначала создается таблица **mobiledata** , а затем к ней добавляется кластеризованный индекс (требуемый базой данных SQL).

    Используйте следующий запрос команду для проверки создания таблицы.

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Должен появиться результат, аналогичный приведенному ниже.

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Enter `exit` at the `1>` , чтобы выйти из служебной программы tsql.

## <a name="sqoop-export"></a>Экспорт Sqoop

1. Используя SSH-подключение к кластеру, выполните следующую команду, чтобы проверить, видна ли база данных SQL в Sqoop:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    При появлении запроса введите пароль пользователя базы данных SQL.

    Эта команда возвращает список баз данных, включая созданную ранее базу данных **sqooptest**.

2. Для экспорта данных из **hivesampletable** в таблицу **mobiledata** используйте следующую команду:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    Эта команда указывает Sqoop подключиться к базе данных **sqooptest**. Затем Sqoop экспортирует данные из **wasb:///hive/warehouse/hivesampletable** в таблицу **mobiledata**.

    > [!IMPORTANT]
    > Используйте `wasb:///`, если хранилище кластера по умолчанию является учетной записью хранения Azure. Используйте `adl:///`, если это Azure Data Lake Store.

3. После выполнения команды используйте следующую команду для подключения к базе данных с помощью TSQL.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P -p 1433 -D sqooptest
    ```

    После установления соединения используйте следующие инструкции для проверки экспорта данных в таблицу **mobiledata** :

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata
    GO
    ```

    Вы увидите список данных в таблице. Введите `exit` для выхода из служебной программы tsql.

## <a name="sqoop-import"></a>Импорт Sqoop

1. Для импорта данных из таблицы **mobiledata** базы данных SQL в каталог **wasb:///tutorials/usesqoop/importeddata** в HDInsight используйте следующую команду:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Поля в данных разделены знаками табуляции, а строки завершаются символом новой строки.

2. После завершения импорта используйте следующую команду для вывода списка данных в новом каталоге:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Использование SQL Server

Sqoop можно также использовать для импорта и экспорта данных из SQL Server в центре обработки данных или на виртуальной машине, размещенной в Azure. Ниже приведены различия между использованием базы данных SQL и SQL Server.

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.

    Пример см. в статье о [подключении HDInsight к локальной сети](./connect-on-premises-network.md).

    Подробные сведения об использовании HDInsight c виртуальными сетями Azure см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md). Дополнительные сведения см. в статье [Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md).

* Также SQL Server должен разрешать аутентификацию SQL. Дополнительные сведения см. в документе [Выбор режима проверки подлинности](https://msdn.microsoft.com/ms144284.aspx).

* Необходимо настроить SQL Server для удаленных соединений. Дополнительные сведения см. в документе [How to troubleshoot connecting to the SQL Server database engine](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (Устранение неполадок при подключении к ядру СУБД SQL Server).

* Создайте базу данных **sqooptest** в SQL Server с помощью служебной программы, например **SQL Server Management Studio** или **tsql**. Инструкции для Azure CLI подходят только для базы данных SQL Azure.

    Используйте следующие инструкции Transact-SQL для создания таблицы **mobiledata**.

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* При подключении к SQL Server из HDInsight может потребоваться использовать IP-адрес SQL Server. Например:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Ограничения

* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.

* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется параметр`-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Oozie с HDInsight][hdinsight-use-oozie]: используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью Hive в HDInsight][hdinsight-analyze-flight-data]: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data]: узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
