<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Использование Hadoop Hive в HDInsight для платформы Azure" metaKeywords="" description="Вы узнаете, как использовать Hive с HDInsight. Файл журнала будет использоваться в качестве входных данных в таблице HDInsight, а HiveQL будет использоваться для запроса данных и подготовки отчета по базовой статистике." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование Hive в HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Использование Hive с Hadoop в HDInsight

[Apache Hive][Apache Hive] обеспечивает возможность выполнения заданий MapReduce через SQL-образный скриптовый язык, называемый *HiveQL*. Hive — это система хранилища данных для Hadoop, которая позволяет производить обобщение и анализ данных большого размера, а также создавать запросы для этих данных. В этой статье вы будете использовать HiveQL для создания запросов на основе образцов данных, которые устанавливаются вместе с распределением кластеров.

**Предварительные требования:**

-   Вы должны были подготовить **кластер HDInsight**. Пошаговое руководство по выполнению этой задачи с помощью портала Azure см. в разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight]. Инструкции по другим способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].

-   Установленная среда **Azure PowerShell** на рабочей станции. Инструкции по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

**Предполагаемое время выполнения:**30 минут

## Содержание

-   [Пример использования Hive][Пример использования Hive]
-   [Загрузка данных для таблиц Hive][Загрузка данных для таблиц Hive]
-   [Выполнение запросов Hive с помощью PowerShell][Выполнение запросов Hive с помощью PowerShell]
-   [Для лучшей производительности используйте Tez][Для лучшей производительности используйте Tez]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="usage"></span></a>Пример использования Hive

![Архитектура HDI.HIVE][Архитектура HDI.HIVE]

Hive структурирует большие объемы неструктурированных данных (в чем и состоит главная задача Hadoop), а затем предоставляет вам возможность создавать запросы с использованием HiveQL. Hive обеспечивает необходимый уровень абстрагирования при работе с платформой приложения MapReduce на базе Java, позволяя пользователям, не владеющим Java и MapReduce, создавать запросы для работы с данными. HiveQL обеспечивает простую оболочку на основе SQL, позволяющую записывать в HiveQL запросы, которые затем компилируются в MapReduce службой HDInsight и выполняются в кластере. Другие преимущества Hive:

-   Hive позволяет программистам, знакомым с платформой MapReduce, подключать пользовательские программы map и reduce для выполнения более сложного анализа, который может не поддерживаться встроенными возможностями языка HiveQL.
-   Hive лучше всего подходит для пакетной обработки больших объемов неизменяемых данных (например, веб-журналов). Но не подходит для приложений обработки транзакций, которым требуется очень маленькое время отклика, например систем управления базами данных.
-   Система Hive оптимизирована для масштабируемости (можно динамически добавлять компьютеры в кластере Hadoop), расширяемости (в структуре MapReduce и с другими интерфейсами программирования) и отказоустойчивости. Задержка не является ключевым вопросом разработки.

## <span id="uploaddata"></span></a>Загрузка данных для таблиц Hive

HDInsight в качестве файловой системы по умолчанию использует хранилище BLOB-объектов Azure для кластеров Hadoop. Во время распределения кластеров несколько файлов-примеров с данными были добавлены в хранилище BLOB-объектов. Вы можете использовать эти файлы-примеры для создания запросов Hive в пределах кластера. При желании вы можете загрузить свой собственный файл в учетную запись хранилища BLOB-объектов для этого кластера. См. раздел [Отправка данных в HDInsight][Отправка данных в HDInsight] для получения более подробной информации. Дополнительные сведения о том, каким образом хранилище BLOB-объектов Azure используется с HDInsight, см. раздел [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

В статье используется файл-пример *log4j*, который устанавливается вместе с кластерами HDInsight и размещается в директории *\\example\\data\\sample.log* под вашим контейнером BLOB-объектов. У вас также есть возможность создавать свои собственные файлы log4j при помощи утилиты для входа в систему [Apache Log4j][Apache Log4j], а затем загружать файлы в контейнер BLOB-объектов. Each log inside the file consists of a line of fields that contains a `[LOG LEVEL]` field to show the type and the severity. Например:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

В этом примере уровень журнала имеет значение "ОШИБКА".

У вас есть возможность получить доступ к файлам с помощью следующего синтаксиса, введенного в поля приложения:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Поэтому для доступа к файлу sample.log, вам необходимо использовать следующий синтаксис:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Замените *mycontainer* на имя контейнера BLOB-объектов Azure, а *mystorage* на имя учетной записи хранилища Azure.

Поскольку файл хранится в файловой системе по умолчанию, можно также получить доступ к файлу, используя следующее:

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> Выполнение запросов Hive с помощью PowerShell

В предыдущем разделе вы определили файл *sample.log*, который будут использовать для выполнения запросов Hive. В этом разделе с помощью HiveQL вы создадите таблицу Hive, загрузите данные в таблицу Hive и создадите запрос для того, чтобы узнать количество журналов ошибок.

В этой статье приводятся инструкции по использованию командлетов Azure PowerShell для выполнения запроса Hive. Перед тем как начать разбираться с этим разделом, необходимо настроить локальную среду, а также настроить подключение к среде Azure, как это описано в разделе **Предварительные требования** в начале этой статьи.

Запросы Hive можно выполнять в PowerShell с помощью командлета **Start-AzureHDInsightJob** или **Invoke-Hive**.

**Выполнение запросов Hive с помощью Start-AzureHDInsightJob**

1.  Откройте окна консоли Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
2.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Задайте переменные в следующем сценарии, затем запустите его:

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Выполните следующий сценарий, чтобы определить запросы HiveQL: Вы можете выбрать между созданием *внутренней* или *внешней* таблицы Hive.

    -   В случае работы с внутренней таблицей образец данных, который вы используете, будет перемещен из текущего места расположения в папку \\hive\\warehouse\\\<*название\_таблицы\>*. Поэтому, когда вы удаляете внутреннюю таблицу, то все связанные с ней данные также будут удалены. Если используется внутренняя таблица, то перед повторным запуском сценария необходимо еще раз отправить файл *sample.log* в хранилище BLOB-объектов.
    -   В случае с внешними таблицами данные не перемещаются из своих изначальных директорий. В ситуации, когда файл данных находится в другом контейнере или другой учетной записи хранения, можно также использовать внешнюю таблицу.

            # HiveQL queries
            # Use the internal table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                           "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                           "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                           "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

            # Use the external table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                            "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                            "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    Команда "УДАЛИТЬ ТАБЛИЦУ" приводит к удалению таблицы и файла данных в том случае, если таблица уже существует. Команда "ЗАГРУЗИТЬ ДАННЫЕ HiveQL" перемещает файл данных из папки "\\example\\data" в папку \\hive\\warehouse\\\<*название\_таблицы\>*.

5.  Выполните следующий сценарий, чтобы создать определение задания Hive:

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    Можно также использовать параметр -File, чтобы указать файл сценария HiveQL в HDFS.

6.  Выполните следующий сценарий, чтобы отправить задание Hive:

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Выполните следующий сценарий, чтобы дождаться завершения задания Hive:

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Выполните следующий сценарий, чтобы напечатать стандартный вывод:

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    Результат:

        [ERROR] 3

    означает, что произошли три случая с записью "ОШИБКА" в файле *sample.log*.

Если это необходимо, вы можете отправить результат вашего запроса в Microsoft Excel для последующего анализа. Более подробно об этом см. в разделе [Соединение Excel с Hadoop с помощью Power Query][Соединение Excel с Hadoop с помощью Power Query].

**Отправка запросов Hive с помощью Invoke-Hive**

1.  Откройте окна консоли Azure PowerShell.
2.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Задайте переменные в следующем сценарии, затем запустите его:

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Подключение к кластеру HDInsight.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  Выполните следующий сценарий, чтобы вызвать запросы HiveQL:

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  Распечатайте результат на консоли.

        # print the output on the console
        Write-Host $response

    Данные результата выглядят следующим образом:

    ![PowerShell Invoke-Hive output][PowerShell Invoke-Hive output]

    Для более длинных запросов HiveQL вы можете использовать PowerShell Here-Strings или файл сценария HiveQL. В приведенном ниже отрывке кода показано, как использовать командлет *Invoke-Hive* для запуска файла сценария HiveQL. Файл сценария HiveQL необходимо передать в WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Дополнительные сведения о конструкции Here-Strings см. в разделе [Использование конструкции Here-Strings в Windows PowerShell][Использование конструкции Here-Strings в Windows PowerShell].

Если это необходимо, вы можете отправить результат вашего запроса в Microsoft Excel для последующего анализа. Более подробно об этом см. в разделе [Соединение Excel с Hadoop с помощью Power Query][Соединение Excel с Hadoop с помощью Power Query].

## <span id="usetez"></span></a>Использование Tez для лучшей производительности.

[Apache Tez][Apache Tez] — это платформа, которая позволяет приложениям, работающим с большими объемами данных, таким как Hive, работать с большей производительностью. В последней версии HDInsight, выполнение заданий на Tez поддерживается Hive. В данный момент эта возможность не задействована по умолчанию, вам требуется ее включить. В будущих версиях программы данная возможность будет задействована по умолчанию. Для того чтобы воспользоваться преимуществами работы с Tez, необходимо установить для запроса Hive следующие значения переменных:

        set hive.execution.engine=tez;
        

Данные характеристики можно настроить для определенных запросов, поместив их в начале кода вашего запроса. Можно задействовать эти характеристики по умолчанию для кластера, если установить значения переменных в настройках одинаковыми со временем создания кластера. За более подробной информацией обращайтесь к разделу [Распределение кластеров HDInsight][Подготовка кластеров HDInsight].

Раздел [Документация по работе Hive на Tez][Документация по работе Hive на Tez] содержит большое количество полезной информации по реализации этого решения и вариантам настроек.

## <span id="nextsteps"></span></a>Дальнейшие действия

Hive упрощает запрос данных с помощью SQL-подобного языка запросов, но другие доступные в HDInsight компоненты предоставляют дополнительные функциональные возможности, такие как перемещение и преобразование данных. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Использование Oozie с HDInsight][Использование Oozie с HDInsight]
-   [Отправка заданий Hadoop программными средствами][Отправка заданий Hadoop программными средствами]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Анализ данных о задержке рейсов с помощью HDInsight][Анализ данных о задержке рейсов с помощью HDInsight]
-   [Документация по пакету Azure HDInsight SDK][Документация по пакету Azure HDInsight SDK]
-   [Отправка данных в HDInsight][Отправка данных в HDInsight]
-   [Приступая к работе с Azure HDInsight][Приступая к работе с HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Пример использования Hive]: #usage
  [Загрузка данных для таблиц Hive]: #uploaddata
  [Выполнение запросов Hive с помощью PowerShell]: #runhivequeries
  [Для лучшей производительности используйте Tez]: #usetez
  [Дальнейшие действия]: #nextsteps
  [Архитектура HDI.HIVE]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [Отправка данных в HDInsight]: ../hdinsight-upload-data/
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Соединение Excel с Hadoop с помощью Power Query]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-connect-excel-power-query/
  [PowerShell Invoke-Hive output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Использование конструкции Here-Strings в Windows PowerShell]: http://technet.microsoft.com/ru-ru/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [Документация по работе Hive на Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Использование Oozie с HDInsight]: ../hdinsight-use-oozie/
  [Отправка заданий Hadoop программными средствами]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
  [Анализ данных о задержке рейсов с помощью HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Документация по пакету Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx
