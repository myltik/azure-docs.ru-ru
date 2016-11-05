---
title: Использование Hadoop Sqoop в HDInsight на основе Linux | Microsoft Docs
description: Узнайте, как запустить импорт и экспорт Sqoop между кластером HDInsight под управлением Linux и базой данных SQL Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: larryfr

---
# Использование Sqoop с Hadoop в HDInsight (SSH)
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Sqoop для импорта и экспорта между кластером HDInsight под управлением Linux и базой данных SQL Azure или базой данных SQL Server.

> [!NOTE]
> В действиях, описанных в этой статье, используется SSH для подключения к кластеру HDInsight под управлением Linux. Клиенты Windows также могут использовать Azure PowerShell и пакет SDK HDInsight для .NET для работы с кластерами Sqoop под управлением Linux. Используйте выбор вкладок, чтобы открыть эти статьи.
> 
> 

## Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Кластер Hadoop в HDInsight** и **база данных SQL Azure**: действия, описанные в этом документе, относятся к кластерам и базам данных, созданным согласно документу [Создание кластера и базы данных SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database). Если у вас уже есть кластер HDInsight и база данных SQL, вы можете заменить ими значения, указанные в этом документе.
* **Рабочая станция**: компьютер с клиентом SSH.

## Установка FreeTDS
1. Используйте SSH для подключения к кластеру HDInsight под управлением Linux. Адрес, используемый при подключении: `CLUSTERNAME-ssh.azurehdinsight.net`, порт: `22`.
   
    Дополнительные сведения об использовании SSH для подключения к HDInsight см. в следующих документах:
   
   * **Клиенты Linux, Unix или OS X**: в разделе [Подключение к кластеру HDInsight под управлением Linux из Linux, OS X или Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)
   * **Клиенты Windows**: в разделе [Подключение к кластеру HDInsight под управлением Linux из Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)
2. Используйте следующую команду для установки FreeTDS:
   
        sudo apt-get --assume-yes install freetds-dev freetds-bin
   
    FreeTDS будет использоваться в нескольких действиях, необходимых для подключения к базе данных SQL.

## Создание таблицы в базе данных SQL
> [!IMPORTANT]
> Если вы используете кластер HDInsight и базу данных SQL, созданную по инструкциям в разделе [Создание кластера и базы данных SQL](hdinsight-use-sqoop.md), не выполняйте действия, описанные в этом разделе, так как, выполняя эти инструкции, вы уже создали базу данных и таблицу.
> 
> 

1. Используя SSH подключение к HDInsight, выполните следующую команду, чтобы установить подключение к базе данных SQL Server и создать таблицу, которая будет использоваться в остальных описанных действиях:
   
        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
   
    Должен появиться результат, аналогичный приведенному ниже.
   
        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>
2. В командной строке `1>` введите следующее:
   
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
   
    Если вводится инструкция `GO`, то оцениваются предыдущие инструкции. Сначала создается таблица **mobiledata**, а затем к ней добавляется кластеризованный индекс (требуемый базой данных SQL).
   
    Используйте следующую команду для проверки создания таблицы:
   
        SELECT * FROM information_schema.tables
        GO
   
    Вы должны увидеть результат, аналогичный приведенному ниже:
   
        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE
3. В командной строке `1>` введите `exit`, чтобы выйти из служебной программы tsql.

## Экспорт Sqoop
1. Используя SSH подключение к HDInsight, выполните следующую команду, чтобы проверить, видна ли баз данных SQL в Sqoop:
   
        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
   
    Эта команда должна вывести список баз данных, включая созданную ранее базу данных **sqooptest**.
2. Для экспорта данных из **hivesampletable** в таблицу **mobiledata** используйте следующую команду:
   
        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
   
    Она дает Sqoop указание на подключение к базе данных SQL, базе данных **sqooptest** и экспорт данных из **wasbs:///hive/warehouse/hivesampletable** (физические файлы для *hivesampletable*) в таблицу **mobiledata**.
3. После выполнения команды используйте следующую команду для подключения к базе данных с помощью TSQL:
   
        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
   
    После установления соединения используйте следующие инструкции для проверки экспорта данных в таблицу **mobiledata**:
   
        SELECT * FROM mobiledata
        GO
   
    Вы увидите список данных в таблице. Введите `exit` для выхода из служебной программы tsql.

## Импорт Sqoop
1. Для импорта данных из таблицы **mobiledata** базы данных SQL в каталог **wasbs:///tutorials/usesqoop/importeddata** в HDInsight используйте следующую команду:
   
        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
   
    В импортированных данных будут содержаться поля, разделенные символом табуляции, а строки будут завершаться символом новой строки.
2. После завершения импорта используйте следующую команду для вывода списка данных в новом каталоге:
   
        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

## Использование SQL Server
Sqoop можно также использовать для импорта и экспорта данных из SQL Server в центре обработки данных или на виртуальной машине, размещенной в Azure. Ниже приведены различия между использованием базы данных SQL и SQL Server.

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.
  
  > [!NOTE]
  > HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не работает с виртуальными сетями на основе территориальных групп.
  > 
  > 
  
    При использовании SQL Server в центре обработки данных необходимо настроить тип соединения виртуальной сети либо как *сеть-сеть*, либо *как точка-сеть*.
  
  > [!NOTE]
  > Для виртуальных сетей с типом соединения **точка-сеть** на SQL Server должно быть запущено приложение настройки VPN-клиента, которое доступно на **панели мониторинга** в конфигурации виртуальной сети Azure.
  > 
  > 
  
    Дополнительную информацию о создании и настройке виртуальных сетей см. в разделе [Задачи настройки виртуальной сети](../services/virtual-machines.md).
* Также SQL Server должен разрешать аутентификацию SQL. Дополнительную информацию см. в разделе [Выбор режима проверки подлинности](https://msdn.microsoft.com/ms144284.aspx).
* Необходимо настроить SQL Server для удаленных соединений. См. [Устранение неполадок при подключении к ядру СУБД SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).
* Необходимо создать базу данных **sqooptest** в SQL Server с помощью служебной программы, например **SQL Server Management Studio** или **tsql**. Инструкции для Azure CLI действительны только для базы данных SQL Azure.
  
    Инструкции TSQL для создания таблицы **mobiledata** аналогичны тем, что используются для базы данных SQL, за исключением создания кластеризованного индекса. Он не является обязательным для SQL Server:
  
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
* При подключении к SQL Server из HDInsight может потребоваться IP-адрес сервера SQL Server, если не настроена служба доменных имен (DNS) для разрешения имен в виртуальной сети Azure. Например:
  
        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

## Ограничения
* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется переключатель `-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## Дальнейшие действия
Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Oozie с HDInsight][hdinsight-use-oozie]\: используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-data]\: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Передача данных в HDInsight][hdinsight-upload-data]\: узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0914_2016-->