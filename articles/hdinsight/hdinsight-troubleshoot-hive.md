---
title: "Устранение неполадок с HIVE в Azure HDInsight | Документация Майкрософт"
description: "Ознакомьтесь с ответами на часто задаваемые вопросы о Hive на платформе Azure HDInsight."
keywords: "Azure HDInsight, Hive, вопросы и ответы, руководство по устранению неполадок, часто задаваемые вопросы"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---

# <a name="hive-troubleshooting"></a>Устранение неполадок с Hive

В этой статье описываются основные проблемы и способы их устранения при работе с полезными данными Hive в Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Как экспортировать хранилище метаданных Hive и импортировать его в другой кластер?

### <a name="issue"></a>Проблема

Нужно экспортировать хранилище метаданных Hive и импортировать его в другой кластер HDInsight.  

### <a name="resolution-steps"></a>Способы устранения 

1. Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH) (ознакомьтесь с дополнительными материалами ниже).
1. Выполните следующую команду в кластере HDInsight, откуда вы хотите экспортировать хранилище метаданных:

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

Это создаст файл с именем `allatables.sql`.

- Скопируйте файл `alltables.sql` в новый кластер HDInsight и выполните команду ниже:

```apache
hive -f alltables.sql
```

Этот код предполагает прежние пути к данным в новом кластере. В противном случае вы можете вручную отредактировать  
файл `alltables.sql` для отображения изменений.

### <a name="further-reading"></a>Дополнительные материалы

- [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>Как найти журналы Hive в кластере?

### <a name="issue"></a>Проблема

Необходимо найти журналы клиента Hive, хранилища метаданных и HiveServer в кластере HDInsight.  

### <a name="resolution-steps"></a>Способы устранения 

- Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH) (ознакомьтесь с дополнительными материалами ниже).
- Журналы клиента Hive можно найти здесь:

```apache
/tmp/<username>/hive.log 
```

- Журналы хранилища метаданных Hive можно найти здесь:

```apache
/var/log/hive/hivemetastore.log 
```

- Журналы HiveServer можно найти здесь:

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>Дополнительные материалы

- [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>Как запустить оболочку Hive с определенными конфигурациями в кластере?

### <a name="issue"></a>Проблема

Необходимо переопределить или указать конфигурации оболочки Hive во время запуска в кластерах HDInsight.  

### <a name="resolution-steps"></a>Способы устранения 

- Укажите пару "ключ — значение" конфигурации во время запуска оболочки Hive (ознакомьтесь с дополнительными материалами ниже):

```apache
hive -hiveconf a=b 
```

- Получите список всех действующих конфигураций оболочки Hive с помощью следующей команды:

```apache
hive> set;
```

Например, используйте следующую команду для запуска оболочки Hive, включив ведение журнала отладки в консоли:
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>Дополнительные материалы

- [Свойства конфигурации Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Как анализировать данные направленного ациклического графа Tez по критическому пути кластера?

### <a name="issue"></a>Проблема

Требуется анализ данных направленного ациклического графа Tez по критическому пути кластера HDInsight.

### <a name="resolution-steps"></a>Способы устранения
 
- Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH) (ознакомьтесь с дополнительными материалами ниже).

- В командной строке выполните следующую команду:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- Получите список других анализаторов, которые можно использовать для анализа данных направленного ациклического графа Tez, с помощью команды ниже:

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

Пример программы должен быть задан в качестве первого аргумента.

Допустимые имена программы: ContainerReuseAnalyzer — печать сведений о повторном использовании контейнера в DAG; CriticalPath — поиск критического пути DAG; LocalityAnalyzer — печать сведений о местоположении в DAG; ShuffleTimeAnalyzer — анализ сведений о смещении времени в DAG; SkewAnalyzer — анализ сведений о неравномерном распределении данных в DAG; SlowNodeAnalyzer — печать сведений об узле в DAG; SlowTaskIdentifier — печать сведений о медленных задачах в DAG; SlowestVertexAnalyzer — печать сведений о медленных вершинах в DAG; SpillAnalyzer — печать сведений о перемещении в DAG; TaskConcurrencyAnalyzer — печать сведений о параллельных задачах в DAG; VertexLevelCriticalPathAnalyzer — поиск критического пути на уровне вершины в DAG.


### <a name="further-reading"></a>Дополнительные материалы

- [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Как скачать данные направленного ациклического графа Tez из кластера?

#### <a name="issue"></a>Проблема

Необходимо скачать данные направленного ациклического графа Tez из кластера HDInsight.

#### <a name="resolution-steps"></a>Способы устранения

Сбор данных направленного ациклического графа Tez можно выполнить двумя способами.

- Из командной строки:
 
    Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH). В командной строке выполните следующую команду:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- Или можно использовать представление Ambari Tez:
   
Перейдите в Ambari, выберите представление Tez (скрыто под значком плитки в правом верхнем углу), щелкните нужную группу обеспечения доступности баз данных, а затем выберите команду скачивания данных.

#### <a name="further-reading"></a>Дополнительные материалы

1) [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








