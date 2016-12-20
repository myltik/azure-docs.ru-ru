---
title: "Введение в экосистему Hadoop в HDInsight. Что такое Hadoop в облаке? | Документация Майкрософт"
description: "Ознакомьтесь с введением в Hadoop, обработкой и анализом распределенных больших данных, а также компонентами экосистемы Hadoop в HDInsight в облаке."
keywords: "анализ больших данных,введение в Hadoop,что такое Hadoop,стек технологий Hadoop,экосистема Hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/21/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 66200ad8688928c368146a177130f6e2fad4849b


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Введение в экосистему Hadoop в Azure HDInsight
 В этой статье представлены вводные сведения о системе Hadoop в Azure HDInsight, ее экосистеме и больших данных. Узнайте о компонентах Hadoop, общей терминологии и сценариях анализа больших данных.

## <a name="what-is-hadoop-on-hdinsight"></a>Что такое Hadoop в HDInsight?
Hadoop — это экосистема программного обеспечения с открытым кодом, которое представляет собой платформу для распределенной обработки, хранения и анализа наборов больших данных в кластерах компьютеров. С помощью Azure HDInsight можно сделать компоненты Hadoop из дистрибутива платформы **Hortonworks Data Platform (HDP)** доступными в облаке, а также развернуть и подготовить управляемые кластеры с высокой степенью надежности и доступности.  

Apache Hadoop был исходным проектом с открытым кодом для обработки больших данных. После него были разработаны связанное программное обеспечение и служебные программы, которые считаются частью стека технологий Hadoop, включая Apache Hive, Apache HBase, Apache Spark и многие другие. Дополнительные сведения см. в разделе [Обзор экосистемы Hadoop в HDInsight](#overview).

## <a name="what-is-big-data"></a>Что такое данные большого объема?
Большие данные — это любой большой массив цифровой информации: от текстов Twitter-канала и данных датчиков промышленного оборудования до информации о просмотренных покупателем страницах и покупках на веб-сайте. Данные большого размера могут быть историческими (т. е. сохраненными за прошлый период) или данными в реальном времени (т. е. транслироваться прямо из источника). Большие данные в различных форматах объединяются в большие тома с постоянно увеличивающейся скоростью.

Чтобы большие данные оказались действительно пригодны для аналитики или выводов, важно подать правильный запрос и собрать нужные сведения. Кроме того, такие данные должны быть доступны, отфильтрованы, проанализированы и представлены в удобном формате. Анализ данных большого размера с помощью Hadoop в HDInsight — это решение, которое может помочь.

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>Обзор экосистемы Hadoop в HDInsight
HDInsight — это облачный дистрибутив быстро развертывающегося стека технологий Apache Hadoop для анализа больших данных. В нем реализованы Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari и т. д. HDInsight совместимо с такими инструментами бизнес-аналитики, как Power BI, Excel, службы аналитики SQL Server и службы отчетов SQL Server.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, Spark, Storm и настраиваемые кластеры
HDInsight предоставляет конфигурации кластера для Apache Hadoop, Spark, HBase или Storm. Кроме того, можно [настроить кластеры с помощью действий сценария](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** предоставляет надежное хранилище данных с [HDFS](#hdfs) и простую модель программирования [MapReduce](#mapreduce) для обработки и анализа данных в параллельном режиме.
* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>** — это платформа параллельной обработки, которая поддерживает обработку в памяти и тем самым значительно повышает производительность приложений, предназначенных для анализа больших данных. Spark можно использовать для SQL, потоковой передачи данных и машинного обучения. См. статью [Обзор. Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-overview.md).
* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** — это база данных NoSQL, созданная на основе Hadoop и обеспечивающая прямой доступ и строгую согласованность для больших объемов неструктурированных и частично структурированных данных (с потенциальным размером таблиц в миллиарды строк и миллионы столбцов). См. обзорную статью [об HBase в HDInsight](hdinsight-hbase-overview.md).
* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** — это распределенная система для вычислений в реальном времени, позволяющая быстро обрабатывать потоки данных большого размера. Storm предлагается в качестве управляемого кластера в HDInsight. См. статью об [анализе данных, передаваемых датчиками в реальном времени, с помощью Storm и Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Пример скриптов настройки
Действия скрипта — это скрипты Bash, запускаемые при подготовке кластера к работе. Их можно использовать для установки дополнительных компонентов в кластере. Для кластеров под управлением Linux используются скрипты Bash.

Ниже приведены примеры скриптов, предоставленных группой разработки HDInsight.

* [Hue](hdinsight-hadoop-hue-linux.md)— это набор веб-приложений, используемых для взаимодействия с кластером. Только кластеры Linux.
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)— это обработка графиков для моделирования отношений между вещами или людьми.
* [R](hdinsight-hadoop-r-scripts-linux.md)— это открытый язык программирования и свободная программная среда для статистических вычислений, используемые в машинном обучении.
* [Solr](hdinsight-hadoop-solr-install-linux.md)— это платформа корпоративного поиска, предоставляющая многофункциональные средства полнотекстового поиска данных.

Сведения о разработке собственных действий скриптов см. в статье [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="what-are-the-hadoop-components-and-utilities"></a>Какие компоненты и служебные программы включает в себя Hadoop?
Кластеры HDInsight включают следующие компоненты и служебные программы.

* **[Ambari](#ambari)**: подготовка, мониторинг кластеров и управление ими, служебные программы.
* **[Avro](#avro)** — библиотека Microsoft .NET для Avro, обеспечивающая сериализацию данных для среды Microsoft .NET.
* **[Hive и HCatalog](#hive)** — запросы, аналогичные SQL-запросам, а также уровень управления таблицами и хранилищами.
* **[Mahout](#mahout)** предназначен для масштабируемых приложений машинного обучения.
* **[MapReduce](#mapreduce)**: устаревшая платформа для распределенной системы обработки и управления ресурсами Hadoop. Платформа ресурсов нового поколения называется [YARN](#yarn).
* **[Oozie](#oozie)**: управление рабочими процессами.
* **[Phoenix](#phoenix)**: уровень реляционной базы данных в HBase.
* **[Pig](#pig)**: более простое создание сценариев для преобразований MapReduce.
* **[Sqoop](#sqoop)**: импорт и экспорт данных.
* **[Tez](#tez)**: обеспечивает эффективную работу ресурсоемких процессов в масштабе.
* **[YARN](#yarn)**: часть основной библиотеки Hadoop и программная платформа MapReduce нового поколения.
* **[ZooKeeper](#zookeeper)**: координация процессов в распределенных системах.

> [!NOTE]
> Дополнительные сведения о конкретных компонентах и их версиях см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?][component-versioning]
> 
> 

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
Apache Ambari используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. Для кластеров HDInsight под управлением Linux можно использовать как пользовательский веб-интерфейс Ambari, так и REST API Ambari, а для кластеров под управлением Windows — подмножество REST API. Представления Ambari в кластерах HDInsight позволяют использовать функции пользовательского интерфейса подключаемых модулей.

См. статьи [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md) (только для Linux), [Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API](hdinsight-monitor-use-ambari-api.md) и <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Справочник по API Apache Ambari</a>.

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (библиотека Microsoft .NET для Avro)
В библиотеке Microsoft .NET для Avro реализуется компактный двоичный формат обмена данными Apache Avro для сериализации данных среды Microsoft.NET. В ней используется формат <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> для определения языково-независимой схемы, которая компенсирует несовместимость языков программирования. То есть данные, сериализированные на одном языке, могут быть прочитаны на другом языке. Подробные сведения о формате см. в <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">спецификации Apache Avro</a>.
Формат файлов Avro поддерживает распределенную модель программирования MapReduce. Файлы "разделяемы", т. е. вы сможете найти нужный фрагмент файла и начать считывание с нужного вам блока. Подробные инструкции см. в статье [Сериализация данных в Hadoop с помощью библиотеки Microsoft Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
Распределенная файловая система Hadoop (HDFS) — это распределенная файловая система, которая вместе с MapReduce и YARN является сердцем экосистемы Hadoop. HDFS — это стандартная файловая система для кластеров Hadoop в HDInsight.

### <a name="a-namehiveahive-hcatalog"></a><a name="hive"></a>Hive и HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> — это программное обеспечение для хранилищ данных на базе Hadoop, которое позволяет создавать запросы и управлять большими наборами данных в распределенном хранилище с использованием языка, аналогичного SQL, под названием HiveQL. Hive, как и Pig, — это абстракция на основе MapReduce. При выполнении Hive преобразует запросы в последовательность заданий MapReduce. Hive концептуально ближе к системам управления реляционными базами данных, чем к Pig, и поэтому подходит для работы с более структурированными данными. Для неструктурированных данных более оптимальным выбором является Pig. См. статью [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> — это уровень управления таблицами и хранилищами для Hadoop, который обеспечивает реляционное представление данных для пользователей. В HCatalog можно читать и записывать файлы в любом формате, для которого можно записать Hive SerDe (сериализатор-десериализатор).

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> — это масштабируемая библиотека алгоритмов машинного обучения, применяемых в Hadoop. Основываясь на статистических принципах, приложения для машинного обучения учат системы анализировать данные и использовать прошлые результаты для программирования дальнейшего поведения. См. статью [Создание списка рекомендуемых фильмов с использованием Apache Mahout и Hadoop в HDInsight](hdinsight-mahout.md).

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
MapReduce — это устаревшая программная платформа Hadoop для написания приложений, выполняющих пакетную обработку больших данных. Задание MapReduce разбивает большие наборы данных и распределяет их на пары "ключ-значение" для последующей обработки.

[YARN](#yarn) — это диспетчер ресурсов и программная платформа Hadoop нового поколения (другое название — MapReduce 2.0). Задания MapReduce выполняются в YARN.

Дополнительную информацию о MapReduce см. в статье <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> на вики-сайте Hadoop.

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> — это система координации рабочих процессов, которая управляет заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop для MapReduce, Pig, Hive и Sqoop. Его также можно использовать для планирования относящихся к системе заданий, таких как Java-программ и скриптов оболочки. См. статью [Использование Oozie с Hadoop для определения и выполнения рабочего процесса в HDInsight](hdinsight-use-oozie.md).

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> — это уровень реляционной базы данных в HBase. Phoenix включает драйвер JDBC, который позволяет пользователям напрямую запрашивать таблицы SQL и управлять ими. Phoenix преобразует запросы и другие инструкции в собственные вызовы API NoSQL — вместо использования MapReduce — что обеспечивает более быструю работу приложений на основе хранилищ NoSQL. См раздел [Использование Apache Phoenix и SQuirreL с кластерами HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> — высокоуровневая платформа, позволяющая MapReduce выполнять сложные преобразования с большими наборами данных при помощи простого языка сценариев Pig Latin. Pig переводит команды, написанные на языке Pig Latin, для использования в Hadoop. Вы можете создавать определяемые пользователем функции для расширения словаря Pig Latin. См. статью [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> — это инструмент, который обеспечивает оптимальную передачу данных большего объема между Hadoop и реляционными базами данных, такими как SQL, или другими структурированными хранилищами данных. См. статью [Использование Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop.md).

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> является платформой приложений, построенной на базе YARN Hadoop, которая выполняет сложные ациклические диаграммы обработки общих данных. Это более гибкий и мощный преемник платформы MapReduce, обеспечивающий более эффективную работу ресурсоемких процессов, таких как Hive, в масштабе. См. раздел ["Использование Apache Tez для повышения производительности" в статье об использовании Hive и HiveQL](hdinsight-use-hive.md#usetez).

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
Apache YARN — это платформа MapReduce (MapReduce 2.0 или MR версии 2), поддерживающая не только пакетную обработку MapReduce, но и другие сценарии обработки данных с большей степенью масштабируемости и обработкой в режиме реального времени. YARN включает диспетчер ресурсов и распределенную платформу приложений. Задания MapReduce выполняются в YARN.

Чтобы узнать больше о YARN, см. статью <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> координирует процессы в больших распределенных системах с помощью общего иерархического пространства имен регистров данных (z-узлов). Znodes содержат небольшие фрагменты мета-данных, необходимых для координации процессов, таких как отслеживание статуса, конфигурации и т. д.

## <a name="programming-languages-on-hdinsight"></a>Языки программирования, поддерживаемые в HDInsight
Кластеры HDInsight — Hadoop, HBase, Storm и Spark — поддерживают несколько языков программирования, но не все из них устанавливаются по умолчанию. Для установки библиотек, модулей или пакетов, не установленных по умолчанию, используйте действие сценария. Платформа ресурсов нового поколения называется [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Поддержка языков программирования по умолчанию
По умолчанию кластеры HDInsight поддерживают следующие языки:

* Java
* Python

Дополнительные языки можно установить с помощью действий сценариев: [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Языки виртуальных машин Java
Многие языки, отличные от Java, могут выполняться на виртуальной машине Java, однако для этого может потребоваться установка дополнительных компонентов в кластер.

В кластерах HDInsight поддерживаются следующие языки, использующие виртуальную машину Java:

* Clojure
* Jython (Python для Java)
* Scala

### <a name="hadoop-specific-languages"></a>Языки для Hadoop
Кластеры HDInsight поддерживают следующие языки, характерные для экосистемы Hadoop:

* Pig Latin для заданий Pig
* HiveQL для заданий Hive и SparkSQL

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>Преимущества Hadoop при работе в облаке
Являясь частью облачной экосистемы Azure, Hadoop в HDInsight обладает определенным количеством преимуществ, а именно:

* Автоматическая подготовка кластеров Hadoop. Кластеры HDInsight создавать намного проще, чем вручную настраивать кластеры Hadoop. Дополнительные сведения см. в статье о [подготовке кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Новейшие компоненты Hadoop. Дополнительные сведения см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?][component-versioning]
* Высокая доступность и надежность кластеров. Дополнительные сведения см. в статье [Доступность и надежность кластеров Hadoop в HDInsight](hdinsight-high-availability-linux.md).
* Эффективное и вместительное хранилище данных BLOB-объектов Azure, версия с поддержкой Hadoop. Дополнительные сведения см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Интеграция с другими службами Azure, включая [веб-приложения](https://azure.microsoft.com/documentation/services/app-service/web/) и [базу данных SQL](https://azure.microsoft.com/documentation/services/sql-database/).
* Дополнительные размеры и типы виртуальных машин для работы с кластерами HDInsight. Дополнительные сведения см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?][component-versioning]
* Масштабирование кластера. Масштабирование кластера позволяет изменить число узлов работающего кластера HDInsight без необходимости его удаления или повторного создания.
* Поддержка виртуальной сети Кластеры HDInsight могут использоваться совместно с виртуальной сетью Azure для обеспечения поддержки облачных ресурсов или гибридных сценариев, когда происходит привязка облачных ресурсов к ресурсам, размещенным в центре обработки данных.
* Низкие первоначальные расходы. Начните с [бесплатной пробной версии](https://azure.microsoft.com/free/) или ознакомьтесь с [подробными сведениями о стоимости HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Более подробно о преимуществах Hadoop в HDInsight можно узнать в статье [Характеристики HDInsight.][marketing-page]

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight "Стандартный" и HDInsight "Премиум"
HDInsight предлагает облачные решения для работы с большими данными в двух категориях: Стандартный и Премиум. HDInsight Стандартный предлагает кластер масштаба предприятия, который организации могут использовать для запуска рабочих нагрузок с большими данными. HDInsight Премиум основан на версии Стандартный и предоставляет дополнительные возможности аналитики и защиты для кластера HDInsight. Дополнительные сведения см. в разделе об [HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

## <a name="a-idresourcesaresources-for-learning-more-about-big-data-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>Ресурсы, посвященные анализу данных большого размера, Hadoop и HDInsight
Для правильного создания ознакомьтесь с этим введением в Hadoop в облаке и анализом больших данных в ресурсах, представленными ниже.

### <a name="hadoop-documentation-for-hdinsight"></a>Документация Hadoop для HDInsight
* [Документация по HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): страница документации по Hadoop в Azure HDInsight со ссылками на статьи, видео и другие ресурсы.
* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linuxt](hdinsight-hadoop-linux-tutorial-get-started.md): краткое руководство по подготовке кластеров HDInsight Hadoop и выполнению примеров запросов Hive.
* [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md): краткое руководство по созданию кластера Spark и выполнению интерактивных запросов Spark SQL.
* [Приступая к работе с R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-get-started.md): начало работы с использованием R Server в HDInsight Premium.
* [Подготовка кластеров HDInsight к работе](hdinsight-hadoop-provision-linux-clusters.md). Узнайте, как подготовить кластер HDInsight Hadoop с помощью портала Azure, интерфейса командной строки Azure или Azure PowerShell.

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: узнайте о библиотеке программного обеспечения Apache Hadoop, платформе, которая позволяет распределять обработку больших наборов данных по компьютерным кластерам.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: узнайте об архитектуре и конструкции распределенной файловой системы Hadoop, основной системы хранения, используемой приложениями Hadoop.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce</a>: узнайте о платформе программирования для написания приложений Hadoop, которые позволяют быстро обрабатывать огромные объемы данных в параллельном режиме в больших кластерах компьютерных узлов.

### <a name="microsoft-business-intelligence"></a>Бизнес-аналитика Майкрософт
Знакомые инструменты бизнес-аналитики, такие как Excel, PowerPivot, SQL Server Analysis Services и SQL Server Reporting Services, позволяют получать, анализировать данные и составлять на их основе отчеты в тесной интеграции с HDInsight с помощью надстройки Power Query или драйвера Microsoft Hive ODBC.

Эти инструменты бизнес-аналитики помогают анализировать данные большого размера.

* [Подключение Excel к HDInsight с помощью Power Query](hdinsight-connect-excel-power-query.md): узнайте о подключении Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером HDInsight, с помощью Microsoft Power Query для Excel. Требуется рабочая станция Windows. Работает с кластерами под управлением Windows или Linux.
* [Подключение Excel к Hadoop с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md). Узнайте об импорте данных из HDInsight с помощью драйвера Microsoft Hive ODBC. Требуется рабочая станция Windows. Работает с кластерами под управлением Windows или Linux.
* [Облачная платформа Майкрософт](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): узнайте о возможностях Power BI для Office 365, скачайте пробную версию SQL Server, а также настройте SharePoint Server 2013 и SQL Server BI.
* [Службы SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).
* [Службы SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Dec16_HO2-->


