---
title: "Устранение неполадок с HBase в Azure HDInsight | Документация Майкрософт"
description: "Устранение причин возникновения пропусков в цепочке регионов."
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017

---

# <a name="hbase-troubleshooting"></a>Устранение неполадок с HBase

В этой статье описываются основные проблемы и способы их устранения при работе с полезными данными HBASE в Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Как запускать отчеты о командах hbck с несколькими неназначенными регионами

Часто при использовании команды hbase hbck пользователи HBase видят сообщение о том, что несколько регионов не назначены или в цепочке регионов есть пропуски.

В интерфейсе HBase Master отобразится несбалансированный список регионов на всех региональных серверах. После этого можно выполнить команду hbase hbck и увидеть пропуски в цепочке регионов.

Сначала следует исправить назначения, так как пропуски могут возникнуть по причине отключенных регионов. 

Выполните следующие действия, чтобы вернуть неназначенные регионы в нормальное состояние.

1. Войдите в кластер HDInsight HBase с помощью SSH.
1. Выполните команду hbase zkcli для подключения к оболочке Zookeeper.
1. Выполните команду rmr /hbase/regions-in-transition или rmr /hbase-unsecure/regions-in-transition.
1. Выйдите из оболочки hbase zkcli с помощью команды exit.
1. Откройте пользовательский интерфейс Ambari и перезапустите службу активного главного узла HBase.
1. Выполните команду hbase hbck еще раз (без дополнительных параметров).

Проверьте выходные данные команды, указанной на шаге 6, и убедитесь, что все регионы назначены.

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Как устранить проблемы со временем ожидания в командах hbck для назначения регионов

### <a name="probable-cause"></a>Возможные причины

Здесь возможной причиной могут быть несколько регионов, которые находятся в состоянии "идет переход" в течение долгого времени. В интерфейсе HBase Master эти регионы могут отображаться как автономные. Из-за большого числа регионов, которые находятся в состоянии "идет переход", будет превышено время ожидания HBase Master и эти регионы не смогут вернуться в оперативное состояние.

### <a name="resolution-steps"></a>Способы устранения

Ниже приведены шаги решения проблемы превышения времени ожидания hbck.

1. Войдите в кластер HDInsight HBase с помощью SSH.
1. Выполните команду hbase zkcli для подключения к оболочке Zookeeper.
1. Выполните команду rmr /hbase/regions-in-transition или rmr /hbase-unsecure/regions-in-transition.
1. Выйдите из оболочки hbase zkcli с помощью команды exit.
1. Откройте пользовательский интерфейс Ambari и перезапустите службу активного главного узла HBase.
1. Снова выполните команду hbase hbck -fixAssignments Это должно устранить проблему.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>Как принудительно отключить безопасный режим HDFS в кластере

### <a name="issue"></a>Проблема

В кластере HDInsight локальная система HDFS зависла в безопасном режиме.   

### <a name="detailed-description"></a>Подробное описание

Не удалось выполнить простую команду HDFS, указанную ниже:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

При попытке запустить указанную выше команду возникла следующая ошибка:

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

Число узлов в кластере HDInsight было недостаточным или близким к коэффициенту репликации HDFS.

### <a name="resolution-steps"></a>Способы устранения 

- Отчет о состоянии HDFS в кластере HDInsight можно получить с помощью следующих команд:

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
- Вы также можете узнать состояние целостности HDFS в кластере HDInsight с помощью следующих команд:

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

- Если вы определили, что отсутствующих, поврежденных, либо нереплицированных блоков нет или что такие блоки можно игнорировать, выполните следующую команду, чтобы вывести узел имен из безопасного режима:

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Как устранить проблемы с подключением JDBC или sqlline к Apache Phoenix

### <a name="resolution-steps"></a>Способы устранения

Для подключения к Apache Phoenix необходимо указать IP-адрес активного узла Zookeeper. Убедитесь, что служба Zookeeper, к которой подключается sqlline.py, запущена и работает.
1. Выполните вход SSH в кластер HDInsight.
1. Выполните следующую команду:
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

Если sqlline.py подключается к Apache Phoenix и время ожидания не истекает, выполните следующую команду, чтобы проверить доступность и работоспособность Apache Phoenix.

```apache
        !tables
        !quit
```      
- Если вышеуказанные команды работают, значит проблема отсутствует. Возможно, неверно указан IP-адрес, предоставленный пользователем.
   
    Тем не менее если команда не выполняется слишком долго, а затем вызывает ошибку, упомянутую ниже, следуйте инструкциям по устранению ниже:

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- Выполните следующие команды на головном узле (hn0) для диагностики состояния таблицы Phoenix SYSTEM.CATALOG.

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- Команда должна вернуть следующую ошибку: 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- Перезапустите службу HMaster на всех узлах Zookeeper с помощью интерфейса Ambari, выполнив следующие шаги:

    а. Перейдите по ссылке HBase -> Active HBase Master (Главный узел HBase) в разделе сводки HBase. 
    а. В разделе Components (Компоненты) перезапустите службу главного узла HBase.
    а. Повторите описанные выше шаги для остальных остановленных служб головного узла HBase в режиме ожидания. 

Для стабилизации и завершения восстановления службы главного узла HBase может потребоваться до пяти минут. Через несколько минут ожидания повторите команды sqlline.py, чтобы убедиться, что таблица системного каталога включена и может запрашиваться. 

Когда таблица SYSTEM. CATALOG будет восстановлена, неполадка с подключением к Apache Phoenix автоматически разрешится.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Что вызывает сбой запуска главного сервера

### <a name="error"></a>Ошибка: 

Сбой атомарной операции переименования

### <a name="detailed-description"></a>Подробное описание

Во время запуска HMaster выполняет многоэтапную инициализацию, включая перемещение данных из временной папки (.tmp) в папку данных, а также проверяет в папке WALs (упреждающее протоколирование) наличие неработающих региональных серверов и т. д. 

В это время для этих папок выполняется основная команда list. Каждый раз, когда в любой из этих папок обнаруживается непредвиденный файл, возникает исключение и сервер не будет запущен.  

### <a name="probable-cause"></a>Возможные причины

Попытайтесь определить временной отрезок создания файла и проверьте в журналах регионального сервера, не произошел ли примерно в это время сбой процесса (обратитесь за помощью в службу поддержки HBase). Это поможет нам предоставить более надежные механизмы защиты от таких ошибок и обеспечить надлежащий процесс завершения работы.

### <a name="resolution-steps"></a>Способы устранения

В такой ситуации проверьте стек вызовов и попытайтесь определить папку, которая может быть причиной этой проблемы (например, папка упреждающего протоколирования или папка .tmp). Затем с помощью Cloud Explorer или команд hdfs попытайтесь найти проблемный файл. Обычно это файл *-renamePending.json (файл журнала, используемый для реализации атомарной операции переименования в драйвере WASB; из-за ошибок при выполнении этой операции такие файлы могут оставаться в случае сбоя процесса и т. д). Принудительно удалите этот файл с помощью Cloud Explorer. 

Кроме того, иногда в этом расположении может существовать временный файл типа $$$. $$$, который не виден через Cloud Explorer, а только с помощью команды hdfs. Для удаления этого файла используйте команду hdfs dfs -rm /<the path>/\$\$\$.\$\$\$.  

Сразу после этого должен запуститься сервер HMaster. 

### <a name="error-no-server-address-listed-in"></a>Ошибка. Отсутствуют адреса серверов 

Отсутствуют адреса серверов в метаданных HBase для региона xxx

### <a name="detailed-description"></a>Подробное описание

Пользователь столкнулся с проблемой в кластере Linux, когда таблица метаданных HBase находится вне сети. При выполнении hbck появилось сообщение "hbase: meta table replicaId 0 is not found on any region" (Таблица метаданных HBase реплики 0 не найдена ни в одном из регионов). После перезапуска HBase не удается инициализировать HMaster. В журналах HMaster появилось сообщение "No server address listed in hbase: meta for region hbase: backup <region name>" (Отсутствуют адреса серверов в метаданных hbase для региона hbase: резервное копирование <имя региона>).  

### <a name="resolution-steps"></a>Способы устранения

- Введите следующую команду в оболочке HBase (при необходимости измените на фактические значения).  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- Удалите запись пространства имен hbase, чтобы та же ошибка не возникла во время сканирования таблицы пространства имен hbase.

- Перезапустите активный сервер HMaster с помощью интерфейса Ambari, чтобы HBase стала работать.  

- Выполните следующую команду в оболочке HBase, чтобы подключиться ко всем автономным таблицам.

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>Дополнительные материалы

[Не удается обработать таблицу HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Ошибка:

Время ожидания HMaster истекает и возникает неустранимое исключение, например java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned (Истекло время ожидания назначения таблицы пространства имен (300 000 мс)).

### <a name="detailed-description"></a>Подробное описание

Очевидно клиенты сталкиваются с этой проблемой при наличии большого количества таблиц и регионов, которые не были очищены при перезапуске служб HMaster. Перезапуск завершился сообщением о сбое, указанным выше. Других ошибок не обнаружено.  

### <a name="probable-cause"></a>Возможные причины

Это известный дефект HMaster. Общие задачи запуска кластера могут занять много времени и HMaster завершает работу, так как таблица пространства имен еще не назначена. Это происходит только в том случае, когда существует большой объем неочищенных данных и 5 минут ожидания недостаточно.
  
### <a name="resolution-steps"></a>Способы устранения

- Войдите в интерфейс Ambari, выберите HBase -> Configs (Конфигурации) и добавьте в пользовательский файл hbase-site.xml следующий параметр: 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- Перезапустите необходимые службы (главным образом HMaster или другие службы HBase).  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Что вызывает сбой перезапуска на сервере региона

### <a name="probable-cause"></a>Возможные причины

Подобную ситуацию можно предотвратить следующим образом. Рекомендуется приостановить интенсивные рабочие нагрузки при планировании перезапуска региональных серверов HBase. Если приложение продолжит подключаться к региональным серверам во время завершения работы, это замедлит перезапуск регионального сервера на несколько минут. Кроме того, рекомендуется очищать все таблицы, следуя указаниям в статье [HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase. Ускорение перезапуска кластера HBase с помощью очистки таблиц).

Если пользователь инициирует операцию перезапуска регионального сервера HBase с помощью интерфейса Ambari, региональные серверы сразу же отключаться и не будут перезапускаться очень длительное время. 

Ниже описано, что происходит в этот момент. 

1. Агент Ambari отправляет запрос на остановку регионального сервера.
1. Агент Ambari ожидает 30 секунд, пока региональный сервер завершит работу надлежащим образом. 
1. Если приложение клиента продолжит подключаться к региональному серверу, он не отключится сразу же, следовательно, время ожидания истечет раньше (30 секунд). 
1. По истечении 30 секунд агент Ambari отправит запрос на принудительное отключение (kill -9) регионального сервера. Это будет указано в журнале ambari-agent.log (в каталоге /var/log/ соответствующего рабочего узла):

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
Из-за аварийного завершения работы порт, связанный с процессом, может не освободиться несмотря на завершение процесса на региональном сервере. В такой ситуации при запуске регионального сервера может возникнуть исключение AddressBindException, как показано в журналах ниже. Это можно проверить в журнале region-server.log в каталоге /var/log/hbase на рабочих узлах, на которых не удается запустить региональный сервер. 

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

В подобных случаях можно выполнить следующие действия: 

- Попробуйте уменьшить нагрузку на региональные серверы HBase перед выполнением перезапуска. 

- Также можно (если предыдущий шаг не помог) попробовать выполнить перезапуск региональных серверов на рабочих узлах вручную с помощью следующих команд:

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



