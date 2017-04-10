---
title: "Основные сведения об Apache Storm в службе HDInsight | Документация Майкрософт"
description: "Введение в Apache Storm в службе HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Основные сведения об Apache Storm в службе HDInsight. Аналитика в реальном времени для Hadoop

Apache Storm в службе HDInsight позволяет создавать в Azure распределенные решения для анализа данных в реальном времени.

Apache Storm — это распределенная отказоустойчивая вычислительная система с открытым исходным кодом, предназначенная для обработки данных в реальном времени с использованием Hadoop. Решения Storm могут также обеспечить гарантированную обработку данных и возможность воспроизвести те данные, которые не прошли удачную обработку в первый раз.

## <a name="how-does-storm-work"></a>Как работает Storm

Apache Storm работает с **топологиями** , а не задания MapReduce, с которыми вы, вероятно, знакомы по HDInsight или Hadoop. Топологии состоят из нескольких компонентов, которые расположены в виде направленного ациклического графа (DAG). На следующей схеме показан поток данных между компонентами в базовой топологии подсчета слов.

![Пример расположения компонентов в топологии Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* Компоненты __spout__ переносят данные в топологию. Они генерируют один или несколько потоков в топологии.

* Компоненты __bolt__ используют потоки, генерируемые элементами spout или другими элементами bolt. Элементы bolt могут дополнительно генерировать новые потоки в топологии. Элементы bolt также отвечают за запись данных в постоянное хранилище, например HDFS или HBase.

## <a name="why-use-storm-on-hdinsight"></a>Преимущества Apache Storm в HDInsight

Использование Apache Storm в HDInsight обеспечивает следующие преимущества:

* Решение выполняется как управляемая служба с доступностью на уровне 99,9 %, гарантируемой в рамках соглашения об уровне обслуживания.

* Простая настройка с помощью скриптов во время или после создания кластера. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

* Выбор языка программирования: компоненты Storm можно написать на различных языках, таких как **Java**, **C#** и **Python**.

    * Интеграция Visual Studio с HDInsight для разработки, администрирования и отслеживания топологий C#. Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Поддержка Java-интерфейса **Trident**. Этот интерфейс позволяет создавать топологии Storm, поддерживающие обработку сообщений в рамках подхода "только один раз", сохраняемость "транзакционных" хранилищ данных, а также набор распространенных операций Stream Analytics.

* Простое масштабирование кластера (добавление или удаление рабочих узлов), не влияющее на выполняющиеся топологии Storm.

* Интеграция со следующими службами Azure:

    * Концентраторы событий

    * Виртуальная сеть

    * База данных SQL

    * Хранилище Azure

    * DocumentDB.

    * Безопасное объединений возможностей нескольких кластеров HDInsight с помощью виртуальной сети Azure для создания конвейеров аналитики на основе кластеров HDInsight, HBase или Hadoop.

Список компаний, использующих Apache Storm в качестве решения для анализа данных в реальном времени, см. в статье [Companies Using Apache Storm](https://storm.apache.org/documentation/Powered-By.html) (Компании, использующие Apache Storm).

Сведения для начала работы с системой Storm см. в статье [Начало работы со Storm в HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Простота создания

Новый кластер Storm в HDInsight можно подготовить за считанные минуты. Сведения о создании кластера Storm см. в статье [Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Простота использования

* __Подключение Secure Shell.__ Головные узлы кластера HDInsight доступны через Интернет по протоколу SSH. SSH позволяет выполнять команды непосредственно в кластере.

  Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Веб-подключение.__ Кластеры HDInsight предоставляют веб-интерфейс Ambari. Он позволяет легко отслеживать, настраивать и администрировать службы в кластере. Storm в HDInsight также предоставляет пользовательский интерфейс Storm, который позволяет отслеживать и контролировать выполнение топологий Storm из браузера.

  См. дополнительные сведения об [управлении HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md), а также о [мониторинге и управлении с помощью пользовательского интерфейса Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell и CLI.__ Обе эти служебные программы командной строки можно использовать для работы с HDInsight и другими службами Azure из клиентской системы.

* __Интеграция с Visual Studio.__ Средства Data Lake для Visual Studio включают шаблоны проектов для создания топологий C# Storm с помощью платформы SCP.Net. Средства Data Lake также предоставляют средства для развертывания, отслеживания и администрирования решений с помощью Storm в HDInsight.

  Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Интеграция с другими службами Azure

* __Azure Data Lake Store.__ Дополнительные сведения об этом элементе см. на примере [использования Azure Data Lake Store и Apache Storm в HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Концентраторы событий Azure.__ Примеры использования концентраторов событий Azure с компонентом Storm см. в следующих документах:

    * [Разработка топологии подсчета слов на основе Java для Storm в HDInsight с помощью Maven](hdinsight-storm-develop-java-topology.md)

    * [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* Примеры использования __базы данных SQL__, __DocumentDB__, __концентратора событий__ и __HBase__ включены как шаблоны в средства Azure Data Lake для Visual Studio. Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Надежность

Система Apache Storm гарантирует, что каждое входящее сообщение будет полностью обработано даже в том случае, когда анализ данных распределен между сотнями узлов.

Функциональные возможности **узла Nimbus** схожи с возможностями службы Hadoop JobTracker. Через **Zookeeper** этот узел назначает задачи другим узлам в кластере. Узлы Zookeeper обеспечивают координацию кластера и упрощают взаимодействие между узлом Nimbus и процессом **контролера** на рабочих узлах. Если один узел обработки выходит из строя, узел Nimbus узнает об этом и назначает задачу и связанные данные другому узлу.

По умолчанию для Apache Storm используется только один узел Nimbus, но кластер Storm в HDInsight работает на двух. При сбое основного узла кластер HDInsight переключится на дополнительный на время восстановления основного узла.

![Схема с контролерами и компонентами Nimbus и Zookeeper](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Масштаб

Количество узлов указывается во время создания кластера. Тем не менее рабочая нагрузка может меняться, и тогда возникает необходимость увеличить или уменьшить размер кластера. Все кластеры HDInsight позволяют изменять количество узлов в кластере даже во время обработки данных.

> [!NOTE]
> Чтобы воспользоваться преимуществами новых узлов, добавленных с помощью масштабирования, необходимо повторно сбалансировать топологии, запущенные до увеличения размера кластера.

## <a name="support"></a>Поддержка

Для кластеров Storm в HDInsight круглосуточно действует полная поддержка корпоративного уровня. Коме того, для кластеров Storm в HDInsight заявлена гарантированная доступность в течение 99,9 % времени. Это означает, что мы гарантируем возможность подключения к кластеру извне в течение как минимум 99,9 % времени.

Дополнительные сведения см. на странице [службы поддержки Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Распространенные варианты использования

Ниже приведены несколько распространенных ситуаций, в которых вам может помочь кластер Apache Storm в HDInsight. 

* Интернет вещей.
* Обнаружение мошенничества.
* Социальная аналитика.
* Извлечение, преобразование и загрузка.
* Мониторинг сетей.
* Поиск
* Взаимодействие через мобильные устройства.

Сценарии из реальной жизни см. в статье о том, [как компании используют Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Разработка

__Средства Data Lake для Visual Studio__ позволяют разработчикам .NET проектировать и реализовывать топологии на языке __C#__. Вы также можете создавать гибридные топологии, в которых используются компоненты Java и C#.

Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Можно также разрабатывать решения __Java__ с помощью интегрированной среды разработки по своему усмотрению. Дополнительные сведения см. в статье [Разработка топологии подсчета слов на основе Java для Storm в HDInsight с помощью Maven](hdinsight-storm-develop-java-topology.md).

Для разработки компонентов Storm также может использоваться Python. Дополнительные сведения см. в статье [Разработка топологий Apache Storm с помощью Python в HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Общие шаблоны разработки

### <a name="guaranteed-message-processing"></a>Гарантированная обработка сообщений

Storm может обеспечить различные уровни гарантированной обработки сообщений. Например, простое приложение Storm гарантирует как минимум одну обработку, в то время как Trident может гарантировать ровно одну обработку.

Дополнительные сведения см. в статье о [гарантированной обработке данных](https://storm.apache.org/about/guarantees-data-processing.html) на сайте apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Шаблон чтения входного кортежа, выдающий значение 0 или больше, а затем непосредственно в конце метод execute, запрашивающий подтверждение входного кортежа, является типичным. Storm обеспечивает интерфейс [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) для автоматизации этого шаблона.

### <a name="joins"></a>Соединения

Объединение потоков данных отличается в разных приложениях. Например, вы можете объединять все кортежи с нескольких потоков в один новый поток или объединять только пакеты кортежей для отдельного окна. В любом случае, объединение может завершиться с помощью [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), который является способом определения того, как кортежи перенаправляются к bolts.

В приведенном ниже примере Java используется fieldsGrouping для перенаправления кортежей из компонентов "1", "2" и "3" в сито **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Пакетная обработка

Storm предоставляет внутренний временной механизм (или временной кортеж), который можно использовать для отправки одного пакета каждые X секунд.

Пример использования временного кортежа из компонента на C# см. здесь: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Если вы пользуетесь Trident, он основан на обработке потоков кортежей.

### <a name="caching"></a>Caching

Для ускорения обработки часто используется кэширование в памяти, при котором в памяти сохраняются часто используемые ресурсы. Так как топология распределяется между несколькими узлами и несколькими процессами в пределах одного узла, вы можете использовать [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). `fieldsGrouping` гарантирует, что кортежи, содержащие поля, используемые для поиска в кэше, всегда перенаправляются к одному и тому же процессу. Эта функция группирования избавит от дублирования записей кэша в процессах.

### <a name="streaming-top-n"></a>Максимальное количество потоков (top N)

Когда ваша топология зависит от расчета значения "первые N", это значение нужно рассчитывать параллельно. Затем объедините результат расчетов в глобальное значение. Это можно сделать с помощью [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), направив отдельные поля в параллельную обработку, а затем — в элемент bolt, который определяет глобальное значение "первые N".

Для расчета значения "Топ N" смотрите следующий пример [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Ведение журналов

Storm использует Apache Log4j для записи информации в журнал. По умолчанию в журнал записывается большой объем данных, и разобраться в этой информации может быть трудно. Чтобы управлять ведением журналов, включите файл конфигурации ведения журналов в топологию Storm.

Пример топологии, в котором показано, как настроить ведение журнала, см. в примере Storm в HDInsight в статье [Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight](hdinsight-storm-develop-java-topology.md).

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены статьи, в которых можно найти дополнительные сведения о решениях для анализа данных в реальном времени с помощью Apache Storm в HDInsight.

* [Начало работы с кластером Storm в HDInsight][gettingstarted]
* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

