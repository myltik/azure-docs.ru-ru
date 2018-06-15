---
title: Apache Sqoop с Hadoop в Azure HDInsight | Документация Майкрософт
description: Сведения об использовании Apache Sqoop для импорта и экспорта между Hadoop в HDInsight и базой данных SQL Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 38b1c57b9ac666bc908df69b29f72fbd5aea0495
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403915"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Использование Apache Sqoop для импорта и экспорта между Hadoop в HDInsight и базой данных SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop для импорта и экспорта между кластером Hadoop в Azure HDInsight и базой данных SQL Azure или базой данных Microsoft SQL Server. В этом руководстве используется команда `sqoop` ​​непосредственно из головного узла кластера Hadoop. В этом документе вы подключитесь к головному узлу, используя SSH, и выполните команды.

> [!IMPORTANT]
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> В инструкциях в этом документе предполагается, что вы уже создали базу данных SQL Azure с именем `sqooptest`.
>
> В этом документе содержатся инструкции T-SQL, используемые для создания таблицы в базе данных SQL и отправки запросов к этой таблице. Эти инструкции можно использовать с базой данных SQL в разных клиентах. Мы рекомендуем использовать следующие клиенты:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * служебная программа [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility).

## <a name="create-the-table-in-sql-database"></a>Создание таблицы в базе данных SQL

> [!IMPORTANT]
> Если вы используете кластер HDInsight и базу данных SQL, созданные при изучении руководства [Использование Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop.md), пропустите действия, описанные в этом разделе. База данных и таблица были созданы при выполнении действий, описанных в документе [Использование Sqoop с Hadoop в HDInsight (SSH)](hdinsight-use-sqoop.md).

Используйте клиент SQL для подключения к базе данных `sqooptest` в базе данных SQL. Затем используйте следующую инструкцию T-SQL, чтобы создать таблицу `mobiledata`:

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

## <a name="sqoop-export"></a>Экспорт Sqoop

1. Подключитесь к кластеру HDInsight с помощью SSH. Например, следующая команда подключается к основному головному узлу кластера с именем `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы проверить, видно ли в Sqoop базу данных SQL, используйте следующую команду:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    При появлении запроса введите пароль пользователя базы данных SQL.

    Эта команда возвращает список баз данных, включая использованную ранее базу данных **sqooptest**.

3. Для экспорта данных из таблицы Hive **hivesampletable** в таблицу **mobiledata** в базе данных SQL используйте следующую команду:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Чтобы убедиться, что данные экспортированы, выполните следующий запрос из клиента SQL для просмотра экспортированных данных:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Эта команда выводит 50 строк, импортированных в таблицу.

## <a name="sqoop-import"></a>Импорт Sqoop

1. Для импорта данных из таблицы **mobiledata** базы данных SQL в каталог **wasb:///tutorials/usesqoop/importeddata** в HDInsight используйте следующую команду:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Поля в данных разделены знаками табуляции, а строки завершаются символом новой строки.

    > [!IMPORTANT]
    > Путь `wasb:///` применяется к кластерам, которые используют службу хранилища Azure в качестве хранилища данных кластера по умолчанию. Если кластеры используют Azure Data Lake Store, применяйте `adl:///`.

2. После завершения импорта используйте следующую команду для вывода списка данных в новом каталоге:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Использование SQL Server

Sqoop можно также использовать для импорта и экспорта данных в SQL Server. Ниже приведены различия между использованием базы данных SQL и SQL Server:

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.

    Пример см. в статье о [подключении HDInsight к локальной сети](./../connect-on-premises-network.md).

    Подробные сведения об использовании HDInsight c виртуальными сетями Azure см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../hdinsight-extend-hadoop-virtual-network.md). Дополнительные сведения см. в статье [Виртуальная сеть Azure](../../virtual-network/virtual-networks-overview.md).

* Также SQL Server должен разрешать аутентификацию SQL. Дополнительные сведения см. в документе [Выбор режима проверки подлинности](https://msdn.microsoft.com/ms144284.aspx).

* Необходимо настроить SQL Server для удаленных соединений. Дополнительные сведения см. в документе [How to troubleshoot connecting to the SQL Server database engine](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (Устранение неполадок при подключении к ядру СУБД SQL Server).

* Используйте следующие инструкции Transact-SQL для создания таблицы **mobiledata**.

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
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Ограничения

* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных на сервер Microsoft SQL Server или в базу данных SQL Azure, не поддерживает операции массовой вставки.

* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется параметр`-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="next-steps"></a>Дополнительная информация

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Oozie с HDInsight](../hdinsight-use-oozie.md): используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью HDInsight](../hdinsight-analyze-flight-delay-data.md): используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Передача данных в HDInsight](../hdinsight-upload-data.md): узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
