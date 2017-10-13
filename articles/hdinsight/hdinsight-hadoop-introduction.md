---
title: "Общие сведения о HDInsight, технологической платформе Hadoop и кластерах (Azure) | Документация Майкрософт"
description: "Обзор HDInsight, а также технологической платформы и компонентов Hadoop, включая Spark, Kafka, Hive, HBase для анализа больших данных."
keywords: "azure hadoop, hadoop azure, введение в hadoop, обзор hadoop, технологическая платформа hadoop, приступая к работе с hadoop, знакомство с hadoop, что такое кластер hadoop, кластер hadoop, для чего используется hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Общие сведения об Azure HDInsight, технологической платформе Hadoop и кластерах Hadoop
 В этой статье приведен обзор Azure HDInsight, а также облачного распределения технологической платформы Hadoop. Вы также узнаете, что такое кластер Hadoop и для чего он используется. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Что такое HDInsight и технологическая платформа Hadoop? 
Azure HDInsight является облачным распределением компонентов Hadoop из [платформы данных Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). Первоначально [Apache Hadoop](http://hadoop.apache.org/) представлял платформу с открытым кодом для распределенной обработки и анализа наборов больших данных в кластерах компьютеров. 


Технологическая платформа Hadoop включает связанные программное обеспечение и служебные программы, включая Apache Hive, HBase, Spark, Kafka и т. д. Просмотреть доступные компоненты стека технологии Hadoop в HDInsight можно в статье [Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?][component-versioning]. Дополнительные сведения о Hadoop в HDInsight см. на странице [возможностей HDInsight в Azure](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>Что такое кластер Hadoop и для чего его используют
Термин *Hadoop* также описывает тип кластера, который включает:

* YARN для планирования заданий и управления ресурсами.
* MapReduce для параллельной обработки.
* Распределенную файловую систему Hadoop (HDFS).
  
В большинстве случаев кластеры Hadoop используются для пакетной обработки хранимых данных. Другие типы кластеров в HDInsight включают дополнительные возможности. К примеру, популярность Spark выросла благодаря ускоренной обработке в памяти. Дополнительные сведения см. в разделе [Обзор экосистемы Hadoop в HDInsight](#overview).

## <a name="what-is-big-data"></a>Что такое данные большого объема?
Большие данные представляют любой большой массив цифровой информации, например:

* Данные датчиков промышленного оборудования.
* Действия клиентов, полученные с веб-сайта.
* Канал новостей Twitter.

Большие данные в различных форматах объединяются в большие тома с высокой скоростью обработки. Они могут быть историческими (т. е. сохраненными за прошлый период) или данными в реальном времени (т. е. транслируемыми из источника). 

## <a name="overview"></a>Типы кластеров в HDInsight
HDInsight включает определенные типы кластера и возможности его настройки, например добавление компонентов, служебных программ или языков.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Кластеры Spark, Kafka, Interactive Query, HBase, настраиваемые кластеры и другие типы
HDInsight предлагает следующие типы кластеров.

* **[Apache Hadoop](https://wiki.apache.org/hadoop)** использует управление ресурсами [HDFS](#hdfs) и [YARN](#yarn), а также простую модель программирования [MapReduce](#mapreduce) для параллельной обработки и анализа пакетных данных.
* **[Apache Spark](http://spark.apache.org/)** — это платформа параллельной обработки, которая поддерживает обработку в памяти и тем самым значительно повышает производительность приложений, предназначенных для анализа больших данных. Spark можно использовать для SQL, потоковой передачи данных и машинного обучения. Дополнительные сведения см. в статье [Обзор. Apache Spark в HDInsight](hdinsight-apache-spark-overview.md).
* **[Apache HBase](http://hbase.apache.org/)** — это база данных NoSQL, созданная на основе Hadoop и обеспечивающая прямой доступ и строгую согласованность для больших объемов неструктурированных и частично структурированных данных (с потенциальным размером таблиц в миллиарды строк и миллионы столбцов). Дополнительные сведения см. в статье [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие BigTable, для Hadoop](hdinsight-hbase-overview.md).
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)** — это сервер для размещения параллельных и распределенных процессов на языке R и управления ими. Благодаря ему специалисты по обработке и анализу данных, статистики и программисты на R могут получать доступ к масштабируемым, распределенным методам аналитики в HDInsight по требованию. Дополнительные сведения см. в статье [Общие сведения об R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)** — это распределенная система для вычислений в реальном времени, позволяющая быстро обрабатывать потоки данных большого размера. Storm предлагается в качестве управляемого кластера в HDInsight. См. статью об [анализе данных, передаваемых датчиками в реальном времени, с помощью Storm и Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Apache Interactive Query или Live Long and Process (предварительная версия)](https://cwiki.apache.org/confluence/display/Hive/LLAP)** выполняет кэширование в памяти для выполнения интерактивных и ускоренных запросов Hive. См. инструкции по [использованию Interactive Query в HDInsight](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)** — это платформа с открытым исходным кодом, которая используется для создания конвейеров и приложений потоковой передачи данных. Kafka также предоставляет функциональные возможности очереди сообщений, с помощью которых можно публиковать потоки данных и подписываться на них. См. статью [Введение в Apache Kafka в HDInsight (предварительная версия)](hdinsight-apache-kafka-introduction.md).

Кроме того, настроить кластеры можно с помощью следующих методов:
* **[Кластеры, присоединенные к домену (предварительная версия)](hdinsight-domain-joined-introduction.md)** — с помощью кластеров, присоединенных к домену Active Directory, можно контролировать доступ к данным и управлять ими.
* **[Пользовательские кластеры с действиями скрипта](hdinsight-hadoop-customize-cluster-linux.md)** — это кластеры со скриптами, которые запускаются при подготовке кластера к работе и устанавливают дополнительные компоненты.

### <a name="example-cluster-customization-scripts"></a>Пример сценариев настройки кластеров
Действия скриптов — это скрипты Bash в Linux, которые выполняются при подготовке кластера и могут использоваться для установки дополнительных компонентов в кластере. 

Ниже приведены примеры скриптов, предоставленных группой разработки HDInsight.

* **[Hue](hdinsight-hadoop-hue-linux.md)** — это набор веб-приложений, используемых для взаимодействия с кластером. Только кластеры Linux.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)** — это обработка графиков для моделирования отношений между вещами или людьми.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)** — это платформа корпоративного поиска, предоставляющая многофункциональные средства полнотекстового поиска данных.

Сведения о разработке собственных действий скриптов см. в статье [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Компоненты и служебные программы в кластерах HDInsight
Кластеры HDInsight включают следующие компоненты и служебные программы:

* **[Ambari](#ambari)**: подготовка, мониторинг кластеров и управление ими, служебные программы.
* **[Avro](#avro)** — библиотека Microsoft .NET для Avro, обеспечивающая сериализацию данных для среды Microsoft .NET. 
* **[Hive и HCatalog](#hive)** — запросы, аналогичные SQL-запросам, а также уровень управления таблицами и хранилищами.
* **[Mahout](#mahout)** предназначен для масштабируемых приложений машинного обучения.
* **[MapReduce](#mapreduce)**: устаревшая платформа для распределенной системы обработки и управления ресурсами Hadoop. Дополнительные сведения см. в разделе [YARN](#yarn).
* **[Oozie](#oozie)**: управление рабочими процессами.
* **[Phoenix](#phoenix)**: уровень реляционной базы данных в HBase.
* **[Pig](#pig)**: более простое создание сценариев для преобразований MapReduce.
* **[Sqoop](#sqoop)**: импорт и экспорт данных.
* **[Tez](#tez)**: обеспечивает эффективную работу ресурсоемких процессов в масштабе.
* **[YARN](#yarn)** — управление ресурсами, которые являются частью основной библиотеки Hadoop.
* **[ZooKeeper](#zookeeper)**: координация процессов в распределенных системах.

> [!NOTE]
> Дополнительные сведения о конкретных компонентах и их версиях см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. Кластеры HDInsight в Linux предоставляют веб-интерфейс Ambari и Ambari REST API. Представления Ambari в кластерах HDInsight позволяют использовать функции пользовательского интерфейса подключаемых модулей.
Дополнительные сведения см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md) и в <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">справочнике по API Apache Ambari</a>.

### <a name="avro"></a>Avro (библиотека Microsoft .NET для Avro)
В библиотеке Microsoft .NET для Avro реализуется компактный двоичный формат обмена данными Apache Avro для сериализации данных среды Microsoft.NET. Он определяет независимый от схемы язык, поэтому данные, сериализованные на одном языке, можно считать на другом. Подробные сведения о формате см. в <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">спецификации Apache Avro</a>. Формат файлов Avro поддерживает распределенную модель программирования MapReduce. Файлы "разделяемы", т. е. вы сможете найти нужный фрагмент файла и начать считывание с нужного вам блока. Подробные инструкции см. в статье [Сериализация данных в Hadoop с помощью библиотеки Microsoft Avro](hdinsight-dotnet-avro-serialization.md). Реализация поддержки кластера под управлением Linux планируется в будущем.

### <a name="hdfs"></a>HDFS
Распределенная файловая система Hadoop (HDFS) — это распределенная файловая система, которая вместе с MapReduce и YARN является ядром технологической платформы Hadoop. HDFS — это стандартная файловая система для кластеров Hadoop в HDInsight. Дополнительные сведения см. в статье [Использование HDFS-совместимой службы хранилища с Hadoop в HDInsight](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive и HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> — это программное обеспечение для хранилищ данных на базе Hadoop, которое позволяет создавать запросы и управлять большими наборами данных в распределенном хранилище с использованием языка, аналогичного SQL, под названием HiveQL. Как и Pig, Hive — это абстракция на основе MapReduce, преобразующая запросы в последовательность заданий MapReduce. Hive концептуально ближе к системам управления реляционными базами данных, чем Pig, и поэтому подходит для работы с более структурированными данными. Для неструктурированных данных более оптимальным выбором является Pig. См. статью [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> — это уровень управления таблицами и хранилищами для Hadoop, который обеспечивает реляционное представление данных. В HCatalog можно считывать и записывать файлы в любом формате, который поддерживается Hive SerDe (сериализатор — десериализатор).

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> — это библиотека алгоритмов машинного обучения, применяемых в Hadoop. Основываясь на статистических принципах, приложения для машинного обучения учат системы анализировать данные и использовать прошлые результаты для программирования дальнейшего поведения. См. статью [Создание списка рекомендуемых фильмов с использованием Apache Mahout и Hadoop в HDInsight](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce — это устаревшая программная платформа Hadoop для написания приложений, выполняющих пакетную обработку больших данных. Задание MapReduce разбивает большие наборы данных и распределяет их на пары "ключ-значение" для последующей обработки. Задания MapReduce выполняются в [YARN](#yarn). Ознакомьтесь с <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> на вики-сайте Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> — это система координации рабочих процессов, которая управляет заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop для MapReduce, Pig, Hive и Sqoop. Его также можно использовать для планирования относящихся к системе заданий, таких как Java-программ и скриптов оболочки. См. статью [Использование Oozie с Hadoop для определения и выполнения рабочего процесса в HDInsight](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> — это уровень реляционной базы данных в HBase. Phoenix включает драйвер JDBC, который позволяет пользователям напрямую запрашивать таблицы SQL и управлять ими. Phoenix преобразует запросы и другие инструкции в собственные вызовы API NoSQL — вместо использования MapReduce — что обеспечивает более быструю работу приложений на основе хранилищ NoSQL. См раздел [Использование Apache Phoenix и SQuirreL с кластерами HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> — высокоуровневая платформа, позволяющая MapReduce выполнять сложные преобразования с большими наборами данных при помощи простого языка сценариев Pig Latin. Pig переводит команды, написанные на языке Pig Latin, для использования в Hadoop. Вы можете создавать определяемые пользователем функции для расширения словаря Pig Latin. См. статью [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> — это инструмент, который обеспечивает оптимальную передачу данных большего объема между Hadoop и реляционными базами данных, такими как SQL, или другими структурированными хранилищами данных. См. статью [Использование Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> является платформой приложений, построенной на базе YARN Hadoop, которая выполняет сложные ациклические диаграммы обработки общих данных. Это более гибкий и мощный преемник платформы MapReduce, обеспечивающий более эффективную работу ресурсоемких процессов, таких как Hive, в масштабе. См. раздел ["Использование Apache Tez для повышения производительности" в статье об использовании Hive и HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN — это платформа MapReduce (MapReduce 2.0 или MR версии 2), поддерживающая не только пакетную обработку MapReduce, но и другие сценарии обработки данных с большей степенью масштабируемости и обработкой в режиме реального времени. YARN включает диспетчер ресурсов и распределенную платформу приложений. Задания MapReduce выполняются в YARN. Дополнительные сведения о YARN см. на странице <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop YARN</a>.

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> координирует процессы в больших распределенных системах с помощью общего иерархического пространства имен регистров данных (z-узлов). Znodes содержат небольшие фрагменты мета-данных, необходимых для координации процессов, таких как отслеживание статуса, конфигурации и т. д. Ознакомьтесь с примером в статье [Использование Apache Phoenix с кластерами HBase под управлением Linux в HDInsight](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Языки программирования, поддерживаемые в HDInsight
Кластеры HDInsight — Spark, HBase, Kafka, Hadoop и другие — поддерживают несколько языков программирования, но не все из них устанавливаются по умолчанию. Для установки библиотек, модулей или пакетов, не установленных по умолчанию, используйте [действие сценария](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Поддержка языков программирования по умолчанию
По умолчанию кластеры HDInsight поддерживают следующие языки:

* Java
* Python

Дополнительные языки можно установить с помощью [действий сценариев](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Языки виртуальных машин Java
Виртуальная машина Java поддерживает многие другие языки, но для этого в некоторых случаях требуется установка дополнительных компонентов в кластер.

В кластерах HDInsight поддерживаются следующие языки, использующие виртуальную машину Java:

* Clojure
* Jython (Python для Java)
* Scala

### <a name="hadoop-specific-languages"></a>Языки для Hadoop
Кластеры HDInsight поддерживают следующие языки, характерные для технологической платформы Hadoop:

* Pig Latin для заданий Pig
* HiveQL для заданий Hive и SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight "Стандартный" и HDInsight "Премиум"
HDInsight предлагает облачные решения для работы с большими данными в двух категориях: Стандартный и Премиум. HDInsight Стандартный предлагает кластер масштаба предприятия, который организации могут использовать для запуска рабочих нагрузок с большими данными. HDInsight уровня "Премиум" основан на выпуске "Стандартный" и предоставляет дополнительные возможности аналитики и защиты для кластера HDInsight. Дополнительные сведения см. в разделе об [HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

## <a name="microsoft-business-intelligence-and-hdinsight"></a>HDInsight и бизнес-аналитика Майкрософт
Знакомые инструменты бизнес-аналитики позволяют получать, анализировать данные и составлять на их основе отчеты в тесной интеграции с HDInsight с помощью надстройки Power Query или драйвера Microsoft Hive ODBC.

* [Подключение Excel к HDInsight с помощью Power Query](hdinsight-connect-excel-power-query.md). Узнайте о подключении Excel к учетной записи хранения Azure, в которой размещаются данные из кластера HDInsight, с помощью Microsoft Power Query для Excel. Требуется рабочая станция Windows. 
* [Подключение Excel к Hadoop с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md). Узнайте об импорте данных из HDInsight с помощью драйвера Microsoft Hive ODBC. Требуется рабочая станция Windows. 
* [Облачная платформа Майкрософт](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): узнайте о возможностях Power BI для Office 365, скачайте пробную версию SQL Server, а также настройте SharePoint Server 2013 и SQL Server BI.
* [Руководства по службам Analysis Services (SSAS)](http://msdn.microsoft.com/library/hh231701.aspx)
* [Службы Reporting Services (SSRS)](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Дальнейшие действия

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linuxt](hdinsight-hadoop-linux-tutorial-get-started.md): краткое руководство по подготовке кластеров HDInsight Hadoop и выполнению примеров запросов Hive.
* [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md): краткое руководство по созданию кластера Spark и выполнению интерактивных запросов Spark SQL.
* [Приступая к работе с R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-get-started.md): начало работы с использованием R Server в HDInsight Premium.
* [Подготовка кластеров HDInsight к работе](hdinsight-hadoop-provision-linux-clusters.md). Узнайте, как подготовить кластер HDInsight Hadoop с помощью портала Azure, интерфейса командной строки Azure или Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/