---
title: Масштабирование размеров кластера в Azure HDInsight | Документация Майкрософт
description: Масштабируйте кластер HDInsight для адаптации к рабочей нагрузке.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 8b76d7d0441a5c1c25ad17b73083ec0e4feef1fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414326"
---
# <a name="scale-hdinsight-clusters"></a>Масштабирование кластеров HDInsight

HDInsight обеспечивает гибкость, предоставляя возможность увеличивать и уменьшать масштаб определенного количества рабочих узлов в кластерах. Это позволяет сжимать кластер в нерабочие часы или в выходные дни, а также разворачивать его при пиковых бизнес-требованиях.

Например, при пакетной обработке раз в день или месяц можно увеличить масштаб кластера HDInsight за несколько минут до запуска запланированного события, чтобы обеспечить достаточный объем памяти и вычислительной мощности ЦП. Масштабирование можно автоматизировать с помощью командлета PowerShell [`Set–AzureRmHDInsightClusterSize`](hdinsight-administer-use-powershell.md#scale-clusters).  Позже после обработки, когда кластер HDInsight не требует интенсивного использования, можно уменьшить его масштаб для меньшего количества рабочих узлов.

* Чтобы масштабировать кластер с помощью [PowerShell](hdinsight-administer-use-powershell.md), выполните команду ниже:

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Чтобы масштабировать кластер с помощью [Azure CLI](hdinsight-administer-use-command-line.md), выполните команду ниже:

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Чтобы масштабировать кластер с помощью [портала Azure](https://portal.azure.com), откройте панель кластера HDInsight, выберите **Изменить масштаб кластера** в меню слева, а затем на панели изменения масштаба кластера введите количество рабочих узлов и нажмите кнопку "Сохранить".

    ![Изменение масштаба кластера](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

С помощью любого из этих методов можно увеличивать или уменьшать масштаб кластера HDInsight за считанные минуты.

## <a name="scaling-impacts-on-running-jobs"></a>Влияние масштабирования на выполнение заданий

**Добавление** узлов в работающий кластер HDInsight не повлияет на какие-либо ожидающие или выполняемые задания. Кроме того, во время масштабирования можно безопасно передать новые задания. Сбой операции масштабирования из-за той или иной причины обрабатывается в безопасном режиме, благодаря чему кластер остается работоспособным.

Однако при уменьшении масштаба кластера путем **удаления** узлов любые ожидающие или выполняемые задания завершатся сбоем по завершении операции масштабирования. Это происходит из-за повторного запуска некоторых служб во время этой операции.

Чтобы устранить эту проблему, можно отложить выполнение заданий перед уменьшением масштаба кластера, вручную завершить выполнение заданий или повторно отправить их после завершения операции масштабирования.

Список ожидающих или выполняемых заданий можно просмотреть с помощью пользовательского интерфейса YARN ResourceManager, выполнив шаги ниже:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева выберите **Обзор**, затем — **Кластеры HDInsight** и свой кластер.
3. На панели кластера HDInsight в меню в верхней области выберите **Панель мониторинга**, чтобы открыть пользовательский интерфейс Ambari. Введите учетные данные входа кластера.
4. В списке служб в меню слева щелкните **YARN**. На странице YARN выберите **Быстрые ссылки** и наведите указатель мыши на активный головной узел, а затем щелкните **пользовательский интерфейс ResourceManager**.

    ![Пользовательский интерфейс ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Прямой доступ к пользовательскому интерфейсу ResourceManager можно получить, щелкнув ссылку `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Вы увидите список заданий и их текущее состояние. На снимке экрана видно одно задание, выполняемое в настоящий момент:

![Приложения пользовательского интерфейса ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Чтобы вручную завершить работу запущенного приложения, из оболочки SSH выполните команду ниже:

```bash
yarn application -kill <application_id>
```

Например: 

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>Повторная балансировка кластера HBase

В течение нескольких минут после завершения операции масштабирования автоматически выполняется балансировка нагрузки на региональные серверы. Чтобы вручную распределить нагрузку между региональными серверами, выполните следующие действия.

1. Подключитесь к кластеру HDInsight по протоколу SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Запустите оболочку HBase:

        hbase shell

3. Используйте команду ниже, чтобы вручную распределить нагрузку между региональными серверами:

        balancer

## <a name="scale-down-implications"></a>Последствия уменьшения масштаба

Как упоминалось ранее, все ожидающие или выполняемые задания завершаются после уменьшения масштаба. Однако могут возникнуть другие потенциальные последствия уменьшения масштаба.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>Узел имени HDInsight продолжает работу в безопасном режиме после операции уменьшения масштаба

![Изменение масштаба кластера](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Если сжать кластер до масштаба одного рабочего узла, как показано на рисунке выше, HDFS может зависнуть в безопасном режиме при перезагрузке рабочих узлов для установки исправлений или сразу же после операции масштабирования.

В основном это происходит, так как в Hive используется несколько файлов `scratchdir` и ожидается по умолчанию три реплики каждого блока, хотя при уменьшении масштаба как минимум до одного рабочего узла возможна только одна реплика. В результате этого файлы в каталоге `scratchdir` становятся *нереплицированными*. Это может привести к тому, что HDFS зависнет в безопасном режиме после перезапуска служб после операции масштабирования.

При попытке уменьшить масштаб HDInsight обращается к интерфейсам управления Ambari для вывода из эксплуатации дополнительных нежелательных рабочих узлов, что приводит к репликации их блоков HDFS на другие интерактивные рабочие узлы, и последующего успешного уменьшения масштаба кластера. HDFS переходит в безопасный режим во время периода обслуживания и выходит из этого режима после завершения масштабирования. Именно на этом этапе HDFS может зависнуть в безопасном режиме.

HDFS настраивается с помощью параметра `dfs.replication` для трех рабочих узлов. Таким образом, блоки пустых файлов являются нереплицированными, если в интерактивном режиме менее трех рабочих узлов, так как не ожидается, что будут доступны три копии каждого блока файла.

Чтобы вывести HDFS из безопасного режима, можно выполнить команду. Например, если вы знаете, что единственной причиной включения безопасного режима являются нереплицированные временные файлы, тогда можно безопасно выйти из такого режима. Это объясняется тем, что нереплицированные файлы представляют собой временные пустые файлы Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

После выхода из безопасного режима можно вручную удалить временные файлы, или подождать, пока Hive в конечном итоге автоматически удалит их.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Примеры ошибок, когда включен безопасный режим

* H070 не удалось открыть сеанс Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **не удается создать каталог** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Узел имени работает в безопасном режиме**. Количество полученных блоков — 75, требуется еще 12 для достижения порога 0,9900 от общего количества блоков — 87. Количество активных узлов данных (10) стало минимальным (0). Безопасный режим автоматически отключится по достижении пороговых значений.

* H100 не удалось отправить базы данных отображения инструкции: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: подключение к hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] завершилось сбоем: **в подключении отказано**.

* H020 не удалось подключиться к hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException: не удалось создать подключение через HTTP-сеанс к http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: подключение к hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] завершилось сбоем: в подключении отказано: org.apache.thrift.transport.TTransportException: не удалось создать подключение через HTTP-сеанс к http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: подключение к hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] завершилось сбоем: **в подключении отказано**.

* Из журналов Hive: предупреждение [основное]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – ошибка запуска HiveServer2 с попытки: 21, повторная попытка через 60 секунд java.lang.RuntimeException: ошибка применения политики авторизации для конфигурации Hive: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **не удается создать каталог** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Узел имени работает в безопасном режиме**.
    Количество полученных блоков — 0, требуется еще 9 для достижения порога 0,9900 от общего количества блоков — 9.
    Количество активных узлов данных (10) стало минимальным (0). **Безопасный режим автоматически отключится по достижении пороговых значений**.
    org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Вы можете изучить журналы узла имени в папке `/var/log/hadoop/hdfs/` примерно в то же время, когда было выполнено масштабирование кластера, чтобы проследить, когда был активирован безопасный режим. Файлам журнала присвоено имя `Hadoop-hdfs-namenode-hn0-clustername.*`.

Основной причиной ошибок выше является зависимость Hive от временных файлов в HDFS во время выполнения запросов. Когда HDFS переходит в безопасный режим, Hive не может выполнять запросы, так как не удается произвести запись в HDFS. Временные файлы в HDFS расположены на локальном диске, подключенном к виртуальным машинам с отдельным рабочим узлом, и реплицируются на другие рабочие узлы с минимальным количеством реплик — три.

Параметр `hive.exec.scratchdir` в Hive можно настроить в `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Просмотр работоспособности и состояния файловой системы HDFS

Вы можете просмотреть отчет о состоянии во всех узлах имени, чтобы определить, находятся ли узлы в безопасном режиме. Чтобы просмотреть отчет, установите SSH-подключение к каждому головному узлу и выполните команду ниже:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Безопасный режим отключен](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> Требуется параметр `-D`, так как в HDInsight файловой системой по умолчанию является служба хранилища Azure или Azure Data Lake Store. Параметр `-D` указывает, что команды выполняются в локальной файловой системе HDFS.

Затем можно просмотреть отчет, в котором содержатся дополнительные сведения о состоянии HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

На снимке экрана ниже показан результат выполнения этой команды в работоспособном кластере, на котором все блоки будут реплицированы в соответствии с ожидаемыми показателями:

![Безопасный режим отключен](./media/hdinsight-scaling-best-practices/report.png)

HDFS поддерживает команду `fsck` для проверки несогласованности в разных файлах, например отсутствующих блоков файла или нереплицированных блоков. Чтобы выполнить команду `fsck` в файлах `scratchdir` (на временном пустом диске), используйте следующее:

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

При выполнении в работоспособной файловой системе HDFS, где отсутствуют нереплицированные блоки, выходные данные будут выглядеть примерно так, как показано ниже:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

С другой стороны, при выполнении команды `fsck` в файловой системе HDFS с несколькими нереплицированными блоками, выходные данные будут выглядеть примерно так, как показано ниже:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Также можно просмотреть состояние HDFS в пользовательском интерфейсе Ambari, выбрав службу **HDFS** в меню слева или щелкнув ссылку `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Состояние HDFS в Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

На активном или резервном узлах имени могут возникнуть одна или больше критических ошибок. Чтобы просмотреть состояние работоспособности блоков узла имени, выберите ссылку узла имени рядом с оповещением.

![Работоспособность блоков узла имени](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Чтобы очистить пустые файлы, что приводит к удалению ошибок репликации в блоке, установите SSH-подключение к каждому головному узлу и выполните команду ниже:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Эта команда может повлиять на работу Hive, если выполнение некоторых заданий не завершено.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Как предотвратить зависание HDInsight в безопасном режиме из-за нереплицированных блоков

Есть несколько способов предотвратить зависание HDInsight в безопасном режиме.

* Остановите все задания Hive перед уменьшением масштаба HDInsight. Кроме того, можно запланировать операцию уменьшения масштаба, чтобы избежать конфликта с запущенными заданиями Hive.
* Вручную очистите пустые файлы в каталоге `tmp` Hive в HDFS, а затем приступите к уменьшению масштаба.
* Выполните уменьшение масштаба HDInsight как минимум до трех рабочих узлов. Старайтесь избежать масштабирования до одного рабочего узла.
* При необходимости выполните команду, чтобы отключить безопасный режим.

В разделах ниже описываются эти параметры.

#### <a name="stop-all-hive-jobs"></a>Остановка всех заданий Hive

Остановите все задания Hive перед уменьшением масштаба до одного рабочего узла. Если запланирована рабочая нагрузка, выполните уменьшение масштаба после завершения работы Hive.

Это позволит свести к минимуму количество пустых файлов (если таковые имеются) в папке tmp.

#### <a name="manually-clean-up-hives-scratch-files"></a>Очистка пустых файлов Hive вручную

Если при работе Hive остались временные файлы, их можно вручную очистить, а затем выполнить уменьшение масштаба, чтобы избежать активации безопасного режима.

1. Остановите службы Hive и убедитесь, что выполнены все задания и запросы.

2. Получите список содержимого каталога `hdfs://mycluster/tmp/hive/`, чтобы проверить, содержатся ли в нем какие-либо файлы:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Ниже приведен пример выходных данных при наличии файлов:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Если эти файлы обработаны Hive, их можно удалить. Просмотрите страницу пользовательского интерфейса Yarn ResourceManager, чтобы убедиться в отсутствии любых запущенных запросов Hive.

    Пример командной строки для удаления файлов из HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Масштабирование HDInsight до трех рабочих узлов

Если происходит частое зависание в безопасном режиме, а шаги выше по какой-либо причине нельзя использовать для устранения этой проблемы, можно выполнить уменьшение масштаба до трех рабочих узлов. Из-за финансовых ограничений это может стать не лучшим решением, если сравнивать, например, с уменьшением масштаба до одного узла. Однако с одним рабочим узлом HDFS не может гарантировать доступность трех реплик данных для кластера.

#### <a name="run-the-command-to-leave-safe-mode"></a>Выполнение команды для отключения безопасного режима

Последним вариантом является выявление редких случаев, когда HDFS переходит в безопасный режим, после чего можно закрыть команду безопасного режима. После определения причины перехода HDFS в безопасный режим — нереплицированных файлов Hive, выполните команду ниже, чтобы отключить этот режим:

* HDInsight на платформе Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight на платформе Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Дополнительная информация

* [Введение в Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Масштабирование кластеров](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md)
