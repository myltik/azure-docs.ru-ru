---
title: Устранение неполадок в Storm с помощью Azure HDInsight | Документация Майкрософт
description: Ответы на часто задаваемые вопросы об использовании Apache Storm с Azure HDInsight.
keywords: Azure HDInsight, Storm, вопросы и ответы, руководство по устранению неполадок, часто задаваемые вопросы
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: ''
editor: ''
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: raviperi
ms.openlocfilehash: 46f07a1512435fd8ad5cae4df1858f948fe017e1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409880"
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Устранение неполадок в Storm с помощью Azure HDInsight

Ознакомьтесь с основными проблемами и их разрешением при работе с полезными данными Apache Storm в Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Как получить доступ к пользовательскому интерфейсу Storm в кластере?
Есть два способа для получения доступа к пользовательскому интерфейсу Storm из браузера:

### <a name="ambari-ui"></a>Пользовательский интерфейс Ambari
1. Перейдите к панели мониторинга Ambari.
2. В списке служб выберите **Storm**.
3. В меню **Quick Links** (Быстрые ссылки) выберите **Storm UI** (Пользовательский интерфейс Storm).

### <a name="direct-link"></a>Прямая ссылка
Доступ к пользовательскому интерфейсу Storm можно получить по следующему URL-адресу:

https://\<DNS-имя кластера\>/stormui

Пример:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Как передать сведения о контрольной точке spout концентратора событий Storm из одной топологии в другую?

При разработке топологий, которые считывают концентраторы событий Azure с помощью JAR-файла spout концентратора событий HDInsight Storm, необходимо развернуть топологию, которая имеет то же имя в новом кластере. Однако нужно сохранить данные контрольных точек, которые были зафиксированы в Apache ZooKeeper на старом кластере.

### <a name="where-checkpoint-data-is-stored"></a>Где хранятся данные контрольных точек
Данные контрольных точек для смещений сохраняются объектом spout концентратора событий в Zookeeper. Имеется два корневых пути:
- внетранзакционные контрольные точки spout хранятся в /eventhubspout;
- данные транзакционных контрольных точек хранятся в /transactional.

### <a name="how-to-restore"></a>Сведения о восстановлении
Чтобы получить скрипты и библиотеки, которые используются для экспорта данных из ZooKeeper, а затем импортируют данные обратно в ZooKeeper с новым именем, см. [примеры HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

В папке lib находятся JAR-файлы, содержащие реализацию для операции экспорта или импорта. Папка bash содержит пример скрипта, который демонстрирует, как экспортировать данные из сервера ZooKeeper в старом кластере, а затем импортировать его на сервер ZooKeeper в новом кластере.

Выполните скрипт [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) из узлов ZooKeeper для экспорта, а затем импорта данных. Обновите скрипт до правильной версии платформы данных Hortonworks Data Platform (HDP). (Мы работаем над универсальностью этих скриптов в HDInsight. Универсальные скрипты могут запускаться с любого узла кластера без необходимости внесения изменений пользователем.)

Команда экспорта записывает метаданные по пути распределенной файловой системы Apache Hadoop (HDFS) (в хранилище BLOB-объектов Azure или хранилище Azure Data Lake Store) в указанном расположении.

### <a name="examples"></a>Примеры

#### <a name="export-offset-metadata"></a>Экспорт метаданных смещения
1. Используйте SSH для перехода в кластер ZooKeeper в кластере, из которого необходимо экспортировать смещение контрольной точки.
2. Выполните следующую команду (после обновления строки версии HDP) для экспорта данных смещения ZooKeeper по пути HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Импорт метаданных смещения
1. Используйте SSH для перехода в кластер ZooKeeper в кластере, из которого необходимо импортировать смещение контрольной точки.
2. Выполните следующую команду (после обновления строки версии HDP) для импорта данных смещения ZooKeeper из пути HDFS /stormmetadata/zkdata на сервер ZooKeeper в целевом кластере:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Удалите метаданные смещения, чтобы топологии могли начать обработку данных с начала или метки времени (на усмотрение пользователя).
1. Используйте SSH для перехода в кластер ZooKeeper в кластере, из которого необходимо удалить смещение контрольной точки.
2. Выполните следующую команду (после обновления строки версии HDP) для удаления всех данных смещения ZooKeeper в текущем кластере:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Как найти двоичные файлы Storm в кластере?
Двоичные файлы Storm для текущего стека HDP находятся в /usr/hdp/current/storm-client. Расположение одинаковое как для головных узлов, так и для рабочих узлов.
 
Для определенных версий HDP в /usr/hdp (например, /usr/hdp/2.5.0.1233/storm) может существовать несколько двоичных файлов. Папка /usr/hdp/current/storm-client представляет символическую ссылку на последнюю версию, запущенную в кластере.

Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) и [Apache Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Как определить топологию развертывания кластера Storm?
Сначала определяются все компоненты, установленные с помощью HDInsight Storm. Кластер Storm состоит из четырех категорий узлов:

* Узлы шлюза
* Головные узлы
* Узлы Zookeeper
* Рабочие узлы
 
### <a name="gateway-nodes"></a>Узлы шлюза
Узел шлюза —это шлюз и служба обратного прокси-сервера, которая предоставляет общий доступ к активной службе управления Ambari. Он также управляет выбором лидера Ambari.
 
### <a name="head-nodes"></a>Головные узлы
Головные узлы Storm запускают следующие службы:
* Nimbus;
* сервер Ambari;
* сервер метрик Ambari;
* сборщик метрик Ambari.
 
### <a name="zookeeper-nodes"></a>Узлы Zookeeper
HDInsight поставляется с кворумом Zookeeper, включающим три узла. Размер кворума является фиксированным и не может быть перенастроен.
 
Службы Storm в кластере настроены для автоматического использования кворума Zookeeper.
 
### <a name="worker-nodes"></a>Рабочие узлы
Рабочие узлы Storm запускают следующие службы:
* Supervisor;
* виртуальные машины Java (JVM) рабочей роли для выполнения топологий;
* агент Ambari.
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Как найти двоичные файлы объекта spout концентратора событий Storm для разработки?
 
Дополнительные сведения об использовании JAR-файлов spout концентратора событий Storm с топологией см. на следующих ресурсах.
 
### <a name="java-based-topology"></a>Топология на основе Java
[Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Топология на основе C# (Mono в кластерах Linux Storm для HDInsight 3.4+)
[Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Последние двоичные файлы spout концентратора событий Storm для кластеров Linux Storm для HDInsight 3.5+
Дополнительные сведения об использовании объекта spout концентратора событий Storm, который работает с кластерами Linux Storm для HDInsight 3.5+, см. в [файле сведений](https://github.com/hdinsight/mvn-repo/blob/master/README.md) репозитория mvn-repo.
 
### <a name="source-code-examples"></a>Примеры исходного кода
См. [примеры](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) того, как выполнять чтение и запись из концентратора событий Azure с помощью топологии Apache Storm (написанной на Java) в кластере Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Как найти файлы конфигурации Storm Log4J в кластерах?
 
Ниже приведены сведения о том, как определить файлы конфигурации Apache Log4J для служб Storm.
 
### <a name="on-head-nodes"></a>На головных узлах
Файл конфигурации Log4J Nimbus расположен здесь: /usr/hdp/\<версия HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>На рабочих узлах
Файл конфигурации супервизора Log4J расположен здесь: /usr/hdp/\<версия HDP\>/storm/log4j2/cluster.xml.
 
Файл конфигурации Log4J для рабочего узла расположен здесь: /usr/hdp/\<версия HDP\>/storm/log4j2/worker.xml.
 
Пример: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml.

### <a name="see-also"></a>См. также
[Устранение неполадок с помощью Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
