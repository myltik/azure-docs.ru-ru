<properties
	pageTitle="Использование Hadoop Sqoop в HDInsight на основе Linux | Microsoft Azure"
	description="Узнайте, как запустить импорт и экспорт Sqoop между кластером HDInsight под управлением Linux и базой данных SQL Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#Использование Sqoop с Hadoop в HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Sqoop для импорта и экспорта между кластером HDInsight под управлением Linux и базой данных SQL Azure или базой данных SQL Server.

> [AZURE.NOTE] В действиях, описанных в этой статье, используется SSH для подключения к кластеру HDInsight под управлением Linux. В клиентах Windows можно также использовать Azure PowerShell для работы в кластерах Sqoop под управлением Linux, как описано в документации [Использование Sqoop с Hadoop в HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##Что такое Sqoop?

Хотя Hadoop оптимально подходит для обработки частично структурированных и неструктурированных данных, таких как журналы и файлы, может существовать необходимость обработки структурированных данных, хранящихся в реляционных базах данных.

[Sqoop][sqoop-user-guide-1.4.4] — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционной базой данных (реляционной СУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в реляционную СУБД. В этом учебнике в качестве реляционной базы данных используется база данных SQL.

Сведения о версиях Sqoop, поддерживаемых в кластерах HDInsight, см. в разделе [Новые возможности версий кластеров, образованных с помощью HDInsight][hdinsight-versions].


##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**: компьютер с клиентом SSH.

- **Azure CLI**: дополнительные сведения см. в разделе [Установка и настройка Azure CLI](../xplat-cli-install.md).

##Ознакомление со сценарием

Кластер HDInsight включает несколько примеров данных. Будет использована таблица Hive с именем **hivesampletable**, которая ссылается на файл данных в ****wasb:///hive/warehouse/hivesampletable**. Эта таблица содержит некоторые данные о мобильных устройствах. Схема таблицы Hive:

| Поле | Тип данных |
| ----- | --------- |
| clientid | string |
| querytime | string |
| market | string |
| deviceplatform | string |
| devicemake | string |
| devicemodel | string |
| state | string |
| country | string |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

Сначала необходимо экспортировать **hivesampletable** в базу данных SQL Azure или в SQL Server в таблицу **mobiledata**, а затем импортировать таблицу обратно в HDInsight в **wasb:///tutorials/usesqoop/importeddata**.


## Создание кластера и базы данных SQL

1. Щелкните следующие изображение, чтобы открыть шаблон ARM на портале Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон ARM хранится в общедоступном контейнере BLOB-объектов: **https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*.
    
    Шаблон ARM вызывает пакет BACPAC для развертывания схем таблиц в базе данных SQL. Пакет BACPAC также находится в общедоступном контейнере BLOB-объектов по адресу: https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Чтобы использовать закрытый контейнер для BACPAC-файлов, используйте следующие значения в шаблоне:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. В колонке "Параметры" заполните следующие поля:

    - **Имя кластера**. Введите имя создаваемого кластера Hadoop.
    - **Имя для входа и пароль кластера**. По умолчанию для входа используется имя admin.
    - **Имя пользователя SSH и пароль**.
    - **Имя для входа на сервер базы данных SQL Azure и пароль**.

    В разделе переменных жестко заданы указанные далее значения.
    
    |Имя учетной записи хранения по умолчанию|<CluterName>store|
    |----------------------------|-----------------|
    |Имя сервера базы данных SQL Azure|<ClusterName>dbserver|
    |Имя базы данных SQL Azure|<ClusterName>db|
    
    Запишите эти значения. Они потребуются позже в данном руководстве.
    
3\. Нажмите кнопку **ОК**, чтобы сохранить параметры.

4\. В колонке **Настраиваемое развертывание** щелкните раскрывающийся список **Группа ресурсов** и выберите пункт **Создать**, чтобы создать новую группу ресурсов. Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и другие связанные ресурсы.

5\. Щелкните **Условия использования**, а затем нажмите кнопку **Создать**.

6\. Нажмите кнопку **Создать**. Вы увидите новый элемент под названием "Идет отправка развертывания для развертывания шаблона". Процесс создания кластера и базы данных SQL занимает около 20 минут.

Если вы решили использовать существующую базу данных SQL Azure или Microsoft SQL Server

- **База данных SQL Azure**: необходимо настроить правило брандмауэра для сервера базы данных SQL Azure, чтобы разрешить доступ с рабочей станции. Инструкции по созданию базы данных Azure SQL и настройке брандмауэра см. в статье [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] По умолчанию в базе данных SQL Azure разрешены подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале Azure. Инструкции по созданию базы данных SQL Azure и настройке правил брандмауэра см. в статье [Создание и настройка базы данных SQL Azure][sqldatabase-create-configue].

- **SQL Server**: если ваш кластер HDInsight находится в той же виртуальной сети Azure, что и SQL Server, эта статья поможет вам разобраться с импортом данных в базу данных SQL Server и их экспортом из нее.

    > [AZURE.NOTE] HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не работает с виртуальными сетями на основе территориальных групп.

    * Сведения о создании и настройке виртуальной сети см. в статье [Задачи настройки виртуальной сети](../services/virtual-machines/).

        * При использовании SQL Server в центре обработки данных необходимо настроить тип соединения виртуальной сети либо как *сеть-сеть*, либо *как точка-сеть*.

            > [AZURE.NOTE] Для виртуальных сетей с типом соединения **точка-сеть** на SQL Server должно быть запущено приложение настройки VPN-клиента, которое доступно на **панели мониторинга** в конфигурации виртуальной сети Azure.

        * При использовании SQL Server на виртуальной машине Azure можно использовать любую конфигурацию виртуальной сети, если виртуальная машина с SQL Server находится в той же виртуальной сети, что и HDInsight.

    * Сведения о создании кластера HDInsight в виртуальной сети см. в статье [Создание кластеров Hadoop в HDInsight с использованием настраиваемых параметров](hdinsight-provision-clusters.md).

    > [AZURE.NOTE] Кроме того, SQL Server должен разрешать проверку подлинности. Для выполнения шагов, описанных в этой статье, вам понадобится использовать данные для входа на SQL Server.
	

##Экспорт Sqoop

2. Используйте следующую команду для создания ссылки на драйвер JDBC для SQL Server из каталога lib в Sqoop. Этот драйвер используется для обмена данными между Sqoop и базой данных SQL:

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. Чтобы проверить, видно ли в Sqoop базу данных SQL, используйте следующую команду:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Эта команда должна вывести список баз данных, включая созданную ранее базу данных **sqooptest**.

4. Для экспорта данных из **hivesampletable** в таблицу **mobiledata** используйте следующую команду:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Она дает Sqoop указание на подключение к базе данных SQL, базе данных **sqooptest** и экспорт данных из ****wasb:///hive/warehouse/hivesampletable** (физические файлы для *hivesampletable*) в таблицу **mobiledata**.

5. После выполнения команды используйте следующую команду для подключения к базе данных с помощью TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    После установления соединения используйте следующие инструкции для проверки экспорта данных в таблицу **mobiledata**:

        SELECT * FROM mobiledata
        GO

    Вы увидите список данных в таблице. Введите `exit` для выхода из служебной программы tsql.

##Импорт Sqoop

1. Для импорта данных из таблицы **mobiledata** базы данных SQL в каталог ****wasb:///tutorials/usesqoop/importeddata** в HDInsight используйте следующую команду:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    В импортированных данных будут содержаться поля, разделенные символом табуляции, а строки будут завершаться символом новой строки.

2. После завершения импорта используйте следующую команду для вывода списка данных в новом каталоге:

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Использование SQL Server

Sqoop можно также использовать для импорта и экспорта данных из SQL Server в центре обработки данных или на виртуальной машине, размещенной в Azure. Ниже приведены различия между использованием базы данных SQL и SQL Server.

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.

    > [AZURE.NOTE] HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не работает с виртуальными сетями на основе территориальных групп.

    При использовании SQL Server в центре обработки данных необходимо настроить тип соединения виртуальной сети либо как *сеть-сеть*, либо *как точка-сеть*.

    > [AZURE.NOTE] Для виртуальных сетей с типом соединения **точка-сеть** на SQL Server должно быть запущено приложение настройки VPN-клиента, которое доступно на **панели мониторинга** в конфигурации виртуальной сети Azure.

    Дополнительную информацию о создании и настройке виртуальных сетей см. в разделе [Задачи настройки виртуальной сети](../services/virtual-machines/).

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

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

- [Использование Oozie с HDInsight][hdinsight-use-oozie]\: используйте действие Sqoop в рабочем процессе Oozie.
- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-data]\: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
- [Передача данных в HDInsight][hdinsight-upload-data]\: узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.



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

<!---HONumber=AcomDC_0323_2016-->