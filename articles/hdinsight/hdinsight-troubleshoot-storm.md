---
title: "Устранение неполадок со Storm в Azure HDInsight | Документация Майкрософт"
description: "Ознакомьтесь с ответами на часто задаваемые вопросы о Storm на платформе Azure HDInsight."
keywords: "Azure HDInsight, Storm, вопросы и ответы, руководство по устранению неполадок, часто задаваемые вопросы"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017

---

# <a name="storm-troubleshooting"></a>Устранение неполадок со Storm

В этой статье описываются основные проблемы и способы их устранения при работе с полезными данными Storm в Apache Ambari.

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>Как получить доступ к пользовательскому интерфейсу Storm в кластере?

### <a name="issue"></a>Проблема
Существует два способа получить доступ к пользовательскому интерфейсу Storm из браузера:

#### <a name="ambari-ui"></a>Пользовательский интерфейс Ambari
1. Перейдите на панель мониторинга Ambari.
1. В списке служб в левой области выберите Storm.
1. Выберите параметр пользовательского интерфейса Storm из раскрывающегося меню быстрых ссылок.

#### <a name="direct-link"></a>Прямая ссылка
Вы можете получить доступ к пользовательскому интерфейсу Storm, используя следующий URL-адрес:

https://\<DNS-имя_кластера\>/stormui

Например, https://stormcluster.azurehdinsight.net/stormui.

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>Как передать сведения о контрольной точке spout концентратора событий Storm из одной топологии в другую?

### <a name="issue"></a>Проблема
При разработке топологий, считывающих данные из концентраторов событий с помощью JAR-файла spout концентраторов событий Storm для HDInsight, как можно развернуть топологию с тем же именем в новом кластере, при этом сохранив данные контрольных точек, зафиксированные в узле Zookeeper в старом кластере?

#### <a name="where-is-checkpoint-data-stored"></a>Где хранятся данные контрольной точки?
Данные контрольной точки для смещений сохраняются объектом spout концентратора событий в Zookeeper. Существует два корневых пути:
- внетранзакционные контрольные точки spout хранятся здесь: /eventhubspout;
- данные контрольных точек spout транзакций хранятся здесь: /transactional.

#### <a name="how-to-restore"></a>Сведения о восстановлении
Скрипты и библиотеки для экспорта данных из Zookeeper и импорта обратно с новым именем можно найти здесь: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0

В папке lib находятся JAR-файлы, содержащие реализацию для операции импорта или экспорта.
В папке bash находится пример скрипта по экспорту данных из сервера Zookeeper старого кластера и их обратному импорту на этот же сервер нового кластера.

Чтобы импортировать или экспортировать данные, необходимо запустить из узлов Zookeeper скрипт [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh).
Его необходимо обновить, чтобы исправить в нем строку версии HDP.
(Команда HDInsight работает над универсальностью этих скриптов, чтобы их можно было запускать с любого узла кластера без необходимости применения изменений пользователем.)

Команда экспорта запишет метаданные в путь HDFS (хранилище BLOB-объектов или ADLS) в определенном расположении.

### <a name="examples"></a>Примеры

##### <a name="export-offset-metadata"></a>Экспорт метаданных смещения
1. Подключитесь по протоколу SSH к кластеру Zookeeper старого кластера, из которого нужно экспортировать смещение контрольной точки.
1. Запустите команду ниже (после обновления строки версии HDP), чтобы экспортировать данные смещения Zookeeper по пути HDFS /stormmetadta/zkdata.

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>Импорт метаданных смещения
1. Подключитесь по протоколу SSH к кластеру Zookeeper старого кластера, из которого нужно экспортировать смещение контрольной точки.
1. Выполните команду ниже (после обновления строки версии HDP), чтобы импортировать данные смещения Zookeeper из пути HDFS /stormmetadata/zkdata на сервер Zookeeper целевого кластера.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>Удалите метаданные смещения, чтобы топологии могли начать обработку данных с начала или метки времени (на усмотрение пользователя).
1. Подключитесь по протоколу SSH к кластеру Zookeeper старого кластера, из которого нужно экспортировать смещение контрольной точки.
1. Выполните команду ниже (после обновления строки версии HDP), чтобы удалить все данные смещения Zookeeper для текущего кластера.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Как найти двоичные файлы Storm в кластере?

### <a name="issue"></a>Проблема
 Сведения о расположении двоичных файлов служб Storm в кластере HDInsight

### <a name="resolution-steps"></a>Способы устранения

Двоичные файлы Storm для текущего стека HDP можно найти здесь: /usr/hdp/current/storm-client

Это расположение является общим для головных и рабочих узлов.
 
В пути /usr/hdp (например, /usr/hdp/2.5.0.1233/storm) может быть несколько двоичных файлов для версии HDP.

Но /usr/hdp/current/storm-client представляет символическую ссылку на последнюю версию, запущенную в кластере.

### <a name="further-reading"></a>Дополнительные материалы
 [Подключение к кластеру HDInsight с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)[Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Как определить топологию развертывания кластера Storm?
 
### <a name="issue"></a>Проблема
 
Определение всех компонентов, установленных с HDInsight Storm.
 
Кластер Storm состоит из 4 категорий узлов:
1. Шлюз
1. Головные узлы
1. Узлы Zookeeper
1. Рабочие узлы
 
#### <a name="gateway-nodes"></a>Узлы шлюза
Служба шлюза и обратного прокси-сервера, которая предоставляет общий доступ к активной службе управления Ambari и управляет выбором лидера Ambari.
 
#### <a name="zookeeper-nodes"></a>Узлы Zookeeper
HDInsight поставляется с кворумом Zookeeper, включающим 3 узла.
Размер кворума является фиксированным и его нельзя настроить.
 
Службы Storm в кластере настроены для автоматического использования кворума Zookeeper.
 
#### <a name="head-nodes"></a>Головные узлы
Головные узлы Storm запускают следующие службы:
1. Nimbus;
1. сервер Ambari;
1. сервер показателей Ambari;
1. сборщик показателей Ambari.
 
#### <a name="worker-nodes"></a>Рабочие узлы
 Рабочие узлы Storm запускают следующие службы:
1. Supervisor;
1. виртуальные машины Java рабочего узла для запущенных топологий;
1. агент Ambari.
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>Как найти двоичные файлы Storm-EventHub-Spout для разработки?
 
### <a name="issue"></a>Проблема
Получение дополнительных сведений об использовании JAR-файлов spout концентратора событий Storm для топологии.
 
#### <a name="msdn-articles-on-how-to"></a>Статьи MSDN с инструкциями
 
##### <a name="java-based-topology"></a>Топология на основе Java
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>Топология на основе C# (использование Mono в кластерах Linux Storm для HDI 3.4+)
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>Последние двоичные файлы spout концентратора событий Storm для кластеров Linux Storm HDI3.5+
Чтобы узнать об использовании последних двоичных файлов spout концентратора событий Storm для кластеров Linux Storm HDI3.5+, используйте эту ссылку: https://github.com/hdinsight/mvn-repo/blob/master/README.md
 
##### <a name="source-code-examples"></a>Примеры исходного кода:
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Как найти файлы конфигурации Storm Log4J в кластерах?
 
### <a name="issue"></a>Проблема
 
Определение файлов конфигурации Log4J для служб Storm.
 
#### <a name="on-headnodes"></a>Для головных узлов:
Файл конфигурации Log4J Nimbus расположен здесь: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
#### <a name="worker-nodes"></a>Рабочие узлы
Файл конфигурации Log4J для узла Supervisor расположен здесь: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
Файл конфигурации Log4J для рабочего узла расположен здесь: /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml
 
Пример: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







