---
title: Устранение неполадок в HBase с помощью Azure HDInsight | Документация Майкрософт
description: Получите ответы на распространенные вопросы о работе с HBase и Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: d5d50121cd0375af1b57baadeb40efb237aaea11
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165287"
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Устранение неполадок в HBase с помощью Azure HDInsight

Ознакомьтесь с основными проблемами и их разрешением при работе с полезными данными Apache HBase в Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Как запускать отчеты с помощью команды hbck с несколькими неназначенными регионами?

При выполнении команды `hbase hbck` часто можно увидеть сообщение об ошибке "multiple regions being unassigned or holes in the chain of regions" (несколько регионов не назначены, или присутствуют пропуски в цепочке регионов).

В пользовательском интерфейсе главного узла HBase можно увидеть количество регионов, которые не сбалансированы по всем региональным серверам. Затем можно выполнить команду `hbase hbck`, чтобы увидеть пропуски в цепочке регионов.

Пропуски могут возникнуть по причине отключенных регионов, поэтому сначала следует исправить назначения. 

Чтобы вернуть неназначенные регионы в нормальное состояние, выполните такие действия:

1. Войдите в кластер HDInsight HBase с помощью SSH.
2. Для подключения к оболочке ZooKeeper выполните команду `hbase zkcli`.
3. Выполните команду `rmr /hbase/regions-in-transition` или `rmr /hbase-unsecure/regions-in-transition`.
4. Для выхода из оболочки `hbase zkcli` используйте команду `exit`.
5. Откройте пользовательский интерфейс Ambari Apache и перезапустите службу активного главного узла HBase.
6. Выполните команду `hbase hbck` еще раз (без каких-либо параметров). Проверьте выходные данные этой команды и убедитесь, что все регионы назначены.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Как устранить проблемы со временем ожидания команд hbck для назначения регионов?

### <a name="issue"></a>Проблема

Потенциальной причиной проблем с временем ожидания при использовании команды `hbck` может быть то, что несколько регионов находятся в состоянии "идет переход" в течение долгого времени. Эти регионы можно увидеть как отключенные в пользовательском интерфейсе главного узла HBase. Так как большое количество областей пытается выполнить переход, главный узел HBase может превысить время ожидания и не сможет вернуть регионы обратно в оперативное состояние.

### <a name="resolution-steps"></a>Способы устранения

1. Войдите в кластер HDInsight HBase с помощью SSH.
2. Для подключения к оболочке ZooKeeper выполните команду `hbase zkcli`.
3. Выполните команду `rmr /hbase/regions-in-transition` или `rmr /hbase-unsecure/regions-in-transition`.
4. Чтобы выйти из оболочки `hbase zkcli`, используйте команду `exit`.
5. Перезапустите службу активного главного узла HBase в пользовательском интерфейсе Ambari.
6. Выполните команду `hbase hbck -fixAssignments` еще раз.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Как принудительно отключить безопасный режим HDFS в кластере?

### <a name="issue"></a>Проблема

Локальная распределенная файловая система Hadoop (HDFS) зависла в безопасном режиме в кластере HDInsight.

### <a name="detailed-description"></a>Подробное описание

Эта ошибка может быть вызвана сбоем при выполнении такой команды HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Ошибка, которую вы могли увидеть при попытке запустить команду, выглядит так:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Возможные причины

Кластер HDInsight был уменьшен до небольшого числа узлов. Число узлов ниже фактора репликации HDFS или близко к нему.

### <a name="resolution-steps"></a>Способы устранения 

1. Получите состояние HDFS в кластере HDInsight, выполнив следующие команды:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Также можно проверить целостность HDFS в кластере HDInsight с помощью следующих команд:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Если вы определили, что отсутствующих, поврежденных, либо нереплицированных блоков нет или что такие блоки можно игнорировать, выполните следующую команду, чтобы вывести узел имен из безопасного режима:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Как устранить проблемы с подключением JDBC или sqlline к Apache Phoenix?

### <a name="resolution-steps"></a>Способы устранения

Чтобы подключиться к Phoenix, необходимо предоставить IP-адрес активного узла ZooKeeper. Убедитесь, что служба Zookeeper, к которой подключается sqlline.py, запущена и работает.
1. Войдите в кластер HDInsight с помощью SSH.
2. Введите следующую команду:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > IP-адрес активного узла ZooKeeper можно получить из пользовательского интерфейса Ambari. Последовательно выберите **HBase** > **Quick Links** (Быстрые ссылки) > **ZK\* (Активно)** > **Zookeeper Info** (Сведения о Zookeeper). 

3. Если sqlline.py подключается к Phoenix и время ожидания не истекает, выполните следующую команду, чтобы проверить доступность и работоспособность Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Если эта команда работает, проблемы нет. IP-адрес, предоставленный пользователем, может быть неправильным. Однако если команда приостанавливается на длительное время, а затем отображается следующая ошибка, перейдите к шагу 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Выполните следующие команды на головном узле (hn0) для диагностики состояния таблицы Phoenix SYSTEM.CATALOG:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Команда должна вернуть следующую ошибку: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. В пользовательском интерфейсе Ambari выполните следующие действия, чтобы перезапустить службу HMaster на всех узлах ZooKeeper:

    1. В разделе **Summary** (Сводка) HBase перейдите к **HBase** > **Active HBase Master** (Активный главный узел HBase). 
    2. В разделе **Components** (Компоненты) перезапустите службу главного узла HBase.
    3. Повторите описанные выше шаги для остальных служб **главного узла HBase в режиме ожидания**. 

Для стабилизации и завершения процесса восстановления службы главного узла HBase может потребоваться до пяти минут. Через несколько минут повторите команды sqlline.py, чтобы убедиться, что таблица SYSTEM.CATALOG включена и может запрашиваться. 

Когда таблица SYSTEM.CATALOG вернется к нормальной работе, проблема подключения к Phoenix должна быть автоматически разрешена.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Что вызывает сбой запуска главного сервера?

### <a name="error"></a>Ошибка 

Возникает сбой атомарной операции переименования.

### <a name="detailed-description"></a>Подробное описание

Во время запуска HMaster выполняет многоэтапную инициализацию, включая перенос данных из временной папки (.tmp) в папку данных, а также проверяет в папке WAL (упреждающее протоколирование) наличие неработающих региональных серверов и т. д. 

Во время запуска HMaster выполняет базовую команду `list` в этих папках. Каждый раз, когда в любой из этих папок обнаруживается непредвиденный файл, возникает исключение и сервер не будет запущен.  

### <a name="probable-cause"></a>Возможные причины

В журналах регионального сервера попытайтесь определить время ожидания создания файла, а затем посмотрите, произошел ли сбой во время создания файла. (Обратитесь в службу поддержки HBase, чтобы она могла помочь вам в этом.) Это поможет нам предоставить более надежные механизмы защиты от таких ошибок и обеспечить надлежащий процесс завершения работы.

### <a name="resolution-steps"></a>Способы устранения

Проверьте стек вызовов и попытайтесь определить папку, которая может быть причиной проблемы (например, это может быть папка WAL или папка .tmp). Затем в Cloud Explorer или с помощью команд HDFS попытайтесь найти файл с ошибкой. Обычно это файл \*-renamePending.json. (Файл \*-renamePending.json является файлом журнала, который используется для реализации атомарной операции переименования в драйвере WASB. Из-за ошибок в реализации такие файлы могут оставаться в случае сбоя процесса и т. д.) Принудительно удалите этот файл в Cloud Explorer или с помощью команд HDFS. 

В этом расположении в некоторых случаях может иметься временный файл с именем наподобие *$$$.$$$*. Чтобы увидеть этот файл, необходимо использовать команду `ls` HDFS. Этот файл не отображается в Cloud Explorer. Чтобы удалить этот файл, используйте команду HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

После выполнения этих команд HMaster должен сразу запуститься. 

### <a name="error"></a>Ошибка

Отсутствуют адреса серверов в *метаданных HBase* для региона xxx.

### <a name="detailed-description"></a>Подробное описание

В кластере Linux может появиться сообщение, которое указывает, что таблица *hbase: meta* находится в автономном режиме. При выполнении `hbck` может появиться сообщение "hbase: meta table replicaId 0 is not found on any region" (Таблица метаданных HBase реплики 0 не найдена ни в одном из регионов). Проблема может заключаться в том, что серверу HMaster не удалось выполнить инициализацию после перезагрузки HBase. В журналах HMaster может появиться сообщение: "No server address listed in hbase: meta for region hbase: backup \<region name\>" (Отсутствуют адреса серверов в метаданных hbase для региона hbase: резервное копирование <имя региона>).  

### <a name="resolution-steps"></a>Способы устранения

1. В оболочке HBase введите следующие команды (при необходимости измените на фактические значения):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Удалите запись *пространства имен HBase*. Эта запись может быть той же ошибкой, о которой сообщалось в отчете при сканировании таблицы *пространства имен HBase*.

3. Чтобы вернуть HBase в состояние выполнения, в пользовательском интерфейсе Ambari перезапустите службу активного главного узла HMaster.  

4. В оболочке HBase выполните следующую команду, чтобы подключиться ко всем автономным таблицам:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Дополнительные материалы

[Не удается обработать таблицу HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table).


### <a name="error"></a>Ошибка

Время ожидания HMaster истекает, и возникает неустранимое исключение следующего вида "java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned" (java.io.IOException: истекло время ожидания назначения таблицы пространства имен (300 000 мс)).

### <a name="detailed-description"></a>Подробное описание

Такая проблема может возникнуть при наличии большого количества таблиц и регионов, которые не были удалены при перезагрузке служб HMaster. Перезапуск может завершиться ошибкой, и вы увидите сообщение об ошибке, которое указано выше.  

### <a name="probable-cause"></a>Возможные причины

Это известная проблема со службой HMaster. Общие задачи запуска кластера могут занять много времени. Служба HMaster завершает работу, так как таблица пространства имен еще не назначена. Это происходит только в том случае, если имеется большой объем неочищенных данных и пять минут ожидания недостаточно.
  
### <a name="resolution-steps"></a>Способы устранения

1. В пользовательском интерфейсе Ambari перейдите к **HBase** > **Configs** (Конфигурации). В пользовательском файле hbase-site.xml file добавьте следующий параметр: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Перезапустите необходимые службы (HMaster или другие службы HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Что вызывает сбой перезапуска на сервере региона?

### <a name="issue"></a>Проблема

Сбой перезапуска на региональном сервере можно предотвратить, следуя рекомендациям. Рекомендуется приостановить действие высокой рабочей нагрузки при планировании перезагрузки региональных серверов HBase. Если приложение продолжит подключаться к региональным серверам во время завершения работы, это замедлит перезапуск регионального сервера на несколько минут. Кроме того рекомендуется сначала очистить все таблицы. Подробные сведения об очистке таблиц см. в статье [HDInsight HBase: How to improve the HBase cluster restart time by flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase: как уменьшить время перезапуска кластера HBase с помощью очистки таблиц).

При запуске операции перезапуска на региональных серверах HBase в пользовательском интерфейсе Ambari региональные серверы будут сразу же отключены, но они не будут немедленно перезапущены. 

Вот, что происходит в этот момент: 

1. Агент Ambari отправляет запрос на завершение работы к региональному серверу.
2. Агент Ambari ожидает 30 секунд, пока региональный сервер завершит работу надлежащим образом. 
3. Если приложение продолжает подключаться к региональному серверу, его работа не будет завершена немедленно. Перед завершением работы должно истечь 30-секундное время ожидания. 
4. Через 30 секунд агент Ambari отправляет команду force-kill (`kill -9`) на региональный сервер. Это можно увидеть в журнале агента Ambari (в каталоге /var/log/ соответствующего рабочего узла):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
Из-за аварийного завершения работы порт, связанный с процессом, может не освободиться несмотря на завершение процесса на региональном сервере. В результате при запуске регионального сервера может возникнуть AddressBindException, как показано в следующих журналах. Это можно проверить это в файле region-server.log в каталоге /var/log/hbase на рабочих узлах, где региональный сервер не запускается. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Способы устранения

1. Попробуйте уменьшить нагрузку на региональные серверы HBase перед выполнением перезапуска. 
2. Также можно (если шаг 1не помог) попробовать выполнить перезапуск региональных серверов на рабочих узлах вручную с помощью следующих команд:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>См. также
[Устранение неполадок с помощью Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
