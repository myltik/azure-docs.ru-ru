<properties
	pageTitle="Что такое Hadoop в HDInsight. Анализ данных большого размера в облаке | Microsoft Azure"
	description="Знакомство с компонентами Hadoop в облаке в HDInsight. Узнайте, как HDInsight использует кластеры Hadoop для управления, анализа и создания отчетов о данных больших размеров."
	keywords="big data,big data analysis,hadoop,introduction to hadoop,what is hadoop"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/03/2015"
   ms.author="cgronlun"/>


# Введение в Hadoop в HDInsight. Анализ данных большого размера и их обработка в облаке

Ознакомьтесь с Hadoop, его экосистемой и работой с данными большого размера в Azure HDInsight. Узнайте, что такое Hadoop в HDInsight и какие компоненты он в себя включает, а также ознакомьтесь с общей терминологией и сценариями анализа данных большого размера. Кроме того, вы можете ознакомиться с документацией, учебниками и ресурсами, которые можно использовать при работе с Hadoop в HDInsight.

## Что такое Hadoop в HDInsight

Azure HDInsight — это служба, которая позволяет развернуть и подготовить в облаке кластеры Apache Hadoop, обеспечивая программную платформу, предназначенную для управления, анализа и составления отчетов по данным большого размера с высокой степенью надежности и доступности. HDInsight использует распределение Hadoop **Hortonworks Data Platform (HDP)**. Говоря о Hadoop, обычно имеют в виду всю экосистему компонентов Hadoop, включая кластеры Storm и HBase, а также другие технологии, относящиеся к Hadoop. Дополнительную информацию см. в разделе [Обзор экосистемы Hadoop в HDInsight](#overview) ниже.


## Что такое данные большого объема?
"Данными большого объема" называют данные, которые объединяются в большие объемы при постоянно увеличивающихся скоростях с использованием всего разнообразия неструктурированных форматов и семантических контекстов.

Данные большого размера — это любой большой массив цифровой информации: от текстов Twitter-канала и данных датчиков промышленного оборудования до информации о просмотренных покупателем страницах и покупках в онлайн-каталоге. Данные большого размера могут быть историческими (т. е. сохраненными за прошлый период) или данными в реальном времени (т. е. транслироваться прямо из источника).

Чтобы данные большого размера представляли ценность в плане аналитики, на основе которой можно принимать решения, важно не только формулировать надлежащие вопросы для сбора соответствующих данных, но и обеспечить доступность, чистку и анализ таких данных с последующим представлением в надлежащей манере. Анализ данных большого размера с помощью Hadoop в HDInsight — это решение, которое может помочь.


## <a name="overview"></a>Обзор экосистемы Hadoop в HDInsight

HDInsight — это облачная реализация быстро развертывающегося стека технологий Apache Hadoop в Microsoft Azure, предоставляющего готовые решения для анализа данных большого размера. В нем реализованы Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari и т. д. HDInsight совместимо с такими инструментами бизнес-аналитики, как Excel, службы аналитики SQL Server и службы отчетов SQL Server.

### Кластеры Linux и Windows

Azure HDInsight позволяет развертывать и подготавливать кластеры Hadoop в облаке, используя **Linux** или **Windows** в качестве базовой операционной системы.

* **HDInsight на платформе Linux (предварительная версия)** — это кластер Hadoop под управлением Ubuntu. Используйте этот кластер, если вы знакомы с Linux или Unix, выполняете миграцию с существующего решения Hadoop под управлением Linux или хотите с легкостью выполнить интеграцию с компонентами экосистемы Hadoop, созданными для Linux.

* **HDInsight на платформе Windows** — это кластер Hadoop под управлением Windows Server. Используйте его, если вы знакомы со средой Windows и вам необходимо выполнить миграцию с существующего решения Hadoop под управлением Windows или интеграцию с .NET или другими решениями для Windows.

В следующей таблице приведено сравнение.

Категория | Hadoop в Linux | Hadoop в Windows
---------| -------------------| --------------------
**ОС кластера** | Ubuntu 12.04 Long Term Support (LTS) | Windows Server 2012 R2
**Тип кластера** | Hadoop | Hadoop, Hbase, Storm
**Развертывание** | Портал Azure, интерфейс командной строки Azure, Azure PowerShell | Портал Azure, интерфейс командной строки Azure, Azure PowerShell, пакет SDK для HDInsight .NET
**Пользовательский интерфейс кластера** | Ambari | Панель мониторинга кластеров
**Удаленный доступ** | Secure Shell (SSH) | Протокол удаленного рабочего стола (RDP)



### Hadoop, HBase, Storm, Spark и настраиваемые кластеры

HDInsight предоставляет конфигурации кластера для Hadoop, HBase, Storm или Spark. Вы также можете <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">настроить кластеры с помощью действий сценария</a>.

* **Hadoop** (рабочая нагрузка "запрос") — предоставляет надежное хранилище данных с [HDFS](#HDFS) и простую модель программирования [MapReduce](#mapreduce) для обработки и анализа данных в параллельном режиме.

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (рабочая нагрузка NoSQL) — это база данных NoSQL, которая создана на основе Hadoop и обеспечивает прямой доступ и строгую согласованность для больших объемов неструктурированных и частично структурированных данных (с потенциальным размером таблиц в миллиарды строк и миллионы столбцов). См. раздел [Общие характеристики HBase в HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (рабочая нагрузка "поток") — это распределенная вычислительная система реального времени для быстрой обработки больших потоков данных. Storm предлагается в качестве управляемого кластера в HDInsight. См. раздел [Анализ данных, передаваемых датчиками в реальном времени, с помощью Storm и Hadoop](hdinsight-storm-sensor-data-analysis.md).

* **<a  target="_blank" href="http://spark.apache.org/">Apache Spark</a>** — это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа данных большого размера. См. раздел [Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md).

## Какие компоненты включает в себя Hadoop

В дополнение к предыдущим общим конфигурациям кластеры HDInsight включают в себя следующие отдельные компоненты.

* **[Ambari](#ambari)**: подготовка, мониторинг кластеров и управление ими.

* **[Avro](#avro)**: библиотека Microsoft .NET для Avro, которая обеспечивает сериализацию данных для среды Microsoft .NET.

* **[Hive & HCatalog](#hive)**: запросы, аналогичные SQL-запросам, а также уровень управления таблицами и хранилищами.

* **[Mahout](#mahout)**: позволяет осуществлять машинное обучение.

* **[MapReduce и YARN](#mapreduce)**: образуют распределенную систему управления обработкой и ресурсами.

* **[Oozie](#oozie)**: управление рабочими процессами.

* **[Phoenix](#phoenix)**: уровень реляционной базы данных в HBase.

* **[Pig](#pig)**: более простое создание сценариев для преобразований MapReduce.

* **[Sqoop](#sqoop)**: импорт и экспорт данных.

* **[Tez](#tez)**: обеспечивает эффективную работу ресурсоемких процессов в масштабе.

* **[ZooKeeper](#zookeeper)**: координация процессов в распределенных системах.

> [AZURE.NOTE]Дополнительную информацию о конкретных компонентах и их версиях см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight.][component-versioning]

###<a name="ambari"></a>Ambari

Apache Ambari используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. См. статьи [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md) (только для Linux), [Мониторинг кластеров Hadoop в HDInsight с помощью API Ambari](hdinsight-monitor-use-ambari-api.md) и <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Справочник по API Apache Ambari</a>.

### <a name="avro"></a>Avro (библиотека Microsoft .NET для Avro)

В библиотеке Microsoft .NET для Avro реализуется компактный двоичный формат обмена данными Apache Avro для сериализации данных среды Microsoft.NET. В ней используется формат <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> для определения языково-независимой схемы, которая компенсирует несовместимость языков программирования. То есть данные, сериализированные на одном языке, могут быть прочитаны на другом языке. Дополнительную информацию об этом формате см. на странице <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Спецификации Аpache Avro</a>. Формат файлов Avro поддерживает распределенную модель программирования MapReduce. Файлы "разделяемы", т. е. вы сможете найти нужный фрагмент файла и начать считывание с нужного вам блока. Более подробно см. раздел [Сериализация данных с помощью библиотеки данных Microsoft .NET для Avro](hdinsight-dotnet-avro-serialization.md)/


### <a name="hdfs"></a>HDFS

Распределенная файловая система Hadoop (HDFS) — это распределенная файловая система, которая вместе с MapReduce и YARN является сердцем экосистемы Hadoop. HDFS — это стандартная файловая система для кластеров Hadoop в HDInsight.

### <a name="hive"></a>Hive и HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> — это программное обеспечение для хранилищ данных на базе Hadoop, которое позволяет создавать запросы и управлять большими наборами данных в распределенном хранилище с использованием языка, аналогичного SQL, под названием HiveQL. Hive, как и Pig, — это абстракция на основе MapReduce. При выполнении Hive преобразует запросы в последовательность заданий MapReduce. Hive концептуально ближе к системам управления реляционными базами данных, чем к Pig, и поэтому подходит для работы с более структурированными данными. Для неструктурированных данных более оптимальным выбором является Pig. См. статью [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> — это уровень управления таблицами и хранилищами для Hadoop, который обеспечивает реляционное представление данных для пользователей. В HCatalog можно читать и записывать файлы в любом формате, для которого можно записать Hive SerDe (сериализатор-десериализатор).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> — это масштабируемая библиотека алгоритмов машинного обучения, применяемых в Hadoop. Основываясь на статистических принципах, приложения для машинного обучения учат системы анализировать данные и использовать прошлые результаты для программирования дальнейшего поведения. См. раздел [Создание рекомендаций по фильмам с использованием Mahout в Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce и YARN
Hadoop MapReduce — это программная платформа для написания приложений, одновременно обрабатывающих большие наборы данных. Задание MapReduce разбивает большие наборы данных и распределяет их на пары "ключ-значение" для последующей обработки.

Apache YARN — это следующее поколение MapReduce (MapReduce 2.0, или MRv2), которое разделяет две главные задачи JobTracker (управление ресурсами и планирование/отслеживание заданий) на разные сущности.

Дополнительную информацию о MapReduce см. в статье <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> на вики-сайте Hadoop. Чтобы узнать больше о YARN, см. статью <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> — это система координации рабочих процессов, которая управляет заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop для MapReduce, Pig, Hive и Sqoop. Его также можно использовать для планирования относящихся к системе заданий, таких как Java-программ и скриптов оболочки. См. раздел [Использование временного координатора Oozie с Hadoop](hdinsight-use-oozie-coordinator-time.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> — это уровень реляционной базы данных в HBase. Phoenix включает драйвер JDBC, который позволяет пользователям напрямую запрашивать таблицы SQL и управлять ими. Phoenix преобразует запросы и другие инструкции в собственные вызовы API NoSQL — вместо использования MapReduce — что обеспечивает более быструю работу приложений на основе хранилищ NoSQL. См раздел [Использование Apache Phoenix и SQuirreL с кластерами HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> — высокоуровневая платформа, позволяющая MapReduce выполнять сложные преобразования с большими наборами данных при помощи простого языка сценариев Pig Latin. Pig переводит команды, написанные на языке Pig Latin, для использования в Hadoop. Вы можете создавать определяемые пользователем функции (UDFs) для расширения словаря Pig Latin. См. раздел [Использование Pig с Hadoop для анализа файла журнала Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> — это инструмент, который обеспечивает оптимальную передачу данных большего объема между Hadoop и реляционными базами данных, такими как SQL, или другими структурированными хранилищами данных. См. раздел [Использование Sqoop с Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> является платформой приложений, построенной на базе YARN Hadoop, которая выполняет сложные ациклические диаграммы обработки общих данных. Это более гибкий и мощный преемник платформы MapReduce, обеспечивающий более эффективную работу ресурсоемких процессов, таких как Hive, в масштабе. См. статью ["Использование Apache Tez для повышения производительности" в разделе "Использование Hive и HiveQL"](hdinsight-use-hive.md#usetez).


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> координирует процессы в больших распределенных системах с помощью общего иерархического пространства имен регистров данных (z-узлов). Znodes содержат небольшие фрагменты мета-данных, необходимых для координации процессов, таких как отслеживание статуса, конфигурации и т. д.

## <a name="advantage"></a>Преимущества Hadoop при работе в облаке

Являясь частью облачной экосистемы Azure, Hadoop в HDInsight обладает определенным количеством преимуществ, а именно:

* Автоматическая подготовка кластеров Hadoop. Кластеры HDInsight создавать намного проще, чем вручную настраивать кластеры Hadoop. Дополнительную информацию см. в статье [Подготовка кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md).

* Новейшие компоненты Hadoop. Дополнительную информацию см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][component-versioning].

* Высокая доступность и надежность кластеров. Более подробно см. раздел [Доступность и надежность кластеров Hadoop в HDInsight](hdinsight-high-availability.md).

* Эффективное и вместительное хранилище данных BLOB-объектов Azure, версия с поддержкой Hadoop. Более подробно см. раздел [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md).

* Интеграция с другими службами Azure, включая [Веб-приложения](../documentation/services/app-service/web/) и [Базу данных SQL](../documentation/services/sql-database/).

* Низкие первоначальные расходы. Начните с [Бесплатной демонстрационной версии ](/pricing/free-trial/)или ознакомьтесь с [Подробной информацией о стоимости HDInsight](/pricing/details/hdinsight/).


Более подробно о преимуществах Hadoop в HDInsight можно узнать в разделе [Характеристики HDInsight][marketing-page].



## <a id="resources"></a>Ресурсы, посвященные анализу данных большого размера, Hadoop и HDInsight

Для правильного создания ознакомьтесь с этим введением в Hadoop в HDInsight и анализ данных большого размера в ресурсах, представленных ниже.


### HDInsight на платформе Linux (предварительная версия)

* [Приступая к работе с HDInsight в Linux](hdinsight-hadoop-linux-tutorial-get-started.md): краткий учебник по подготовке кластеров HDInsight Hadoop в Linux и по выполнению примеров запросов Hive.

* [Подготовка HDInsight в Linux с использованием настраиваемых параметров](hdinsight-hadoop-provision-linux-clusters.md): узнайте, как подготовить кластер HDInsight Hadoop в Linux с использованием настраиваемых параметров с помощью портала Azure, интерфейса командной строки Azure или Azure PowerShell.

* [Работа с HDInsight в Linux](hdinsight-hadoop-linux-information.md): краткие советы по работе с кластерами Hadoop под управлением Linux, подготовленными в Azure.

* [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md): узнайте, как выполнять управление и мониторинг кластера Hadoop HDInsight под управлением Linux, используя веб-интерфейс Ambari или REST API Ambari.


### HDInsight на платформе Windows

* [Документация по HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/): страница документации по Azure HDInsight со ссылками на статьи, видео и другие ресурсы.

* [Учебный план по HDInsight](hdinsight-learn-map.md): обзор документации по Hadoop для HDInsight.

* [Приступая к работе с Azure HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md): краткий учебник по использованию Hadoop в HDInsight.

* [Выполнение примеров HDInsight](hdinsight-run-samples.md): учебник по выполнению примеров, которые поставляются вместе с HDInsight.

* [Пакет SDK для Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx): справочная документация по пакету SDK для HDinsight.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: узнайте о библиотеке программного обеспечения Apache Hadoop, платформе, которая позволяет распределять обработку больших наборов данных по компьютерным кластерам.

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a>: узнайте об архитектуре и конструкции распределенной файловой системы Hadoop, основной системы хранения, используемой приложениями Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce</a>: узнайте о платформе программирования для написания приложений Hadoop, которые позволяют быстро обрабатывать огромные объемы данных в параллельном режиме в больших кластерах компьютерных узлов.

### База данных SQL в Azure

* [База данных SQL Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): документация MSDN по базе данных SQL.

* [Портал управления для базы данных SQL](https://msdn.microsoft.com/library/azure/dn771027.aspx): легкое и простое в использовании средство управления базами данных для управления базой данных SQL в облаке.

* [Adventure Works для базы данных SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): страница скачивания примеров базы данных SQL.

### Бизнес-аналитика Майкрософт (для HDInsight на платформе Windows)

Знакомые инструменты бизнес-аналитики, такие как Excel, PowerPivot, SQL Server Analysis Services и SQL Server Reporting Services, позволяют получать, анализировать данные и составлять на их основе отчеты в тесной интеграции с HDInsight с помощью надстройки Power Query или драйвера Microsoft Hive ODBC.

Эти инструменты бизнес-аналитики помогают анализировать данные большого размера.

* [Подключение Excel к HDInsight с помощью Power Query](hdinsight-connect-excel-power-query.md): узнайте о подключении Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером HDInsight, с помощью Microsoft Power Query для Excel.

* [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-ODBC-driver.md): узнайте об импорте данных из HDInsight с помощью драйвера Microsoft Hive ODBC.

* [Облачная платформа Майкрософт](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): узнайте о возможностях Power BI для Office 365, скачайте пробную версию SQL Server, а также настройте SharePoint Server 2013 и SQL Server BI.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">Дополнительная информация о службах анализа SQL Server</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Дополнительная информация о службах отчетности SQL Server</a>.


### Решения Hadoop для анализа данных большого размера (для HDInsight на платформе Windows)

Используйте анализ данных большого размера, чтобы проанализировать данные своей организации для лучшего понимания бизнеса. Ниже приведены некоторые примеры:

* [Анализ данных HVAC, передаваемых датчиками](hdinsight-hive-analyze-sensor-data.md): узнайте, как анализировать данные датчиков с помощью Hive с HDInsight (Hadoop) и наглядно отображать результаты в Microsoft Excel. В этом примере вы воспользуетесь Hive для обработки исторических данных, созданных системами HVAC, для того чтобы понять, какая из систем наиболее надежна в поддержании необходимой температуры.

* [Использование Hive в HDInsight для анализа журналов веб-сайтов](hdinsight-hive-analyze-website-log.md): узнайте, как использовать HiveQL в HDInsight для анализа журналов веб-сайта с целью получения точной информации о частоте посещений за день с других веб-сайтов, а также об ошибках, с которыми сталкиваются посетители веб-сайта.

* [Анализ данных датчиков в реальном времени с помощью Storm и HBase в HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): узнайте, как создать решения, которые используют кластер Storm в HDInsight для обработки данных датчиков из концентраторов событий Azure, а затем отображают эти данные в режиме, близком к реальному времени, на веб-панели мониторинга.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
 

<!---HONumber=Sept15_HO2-->