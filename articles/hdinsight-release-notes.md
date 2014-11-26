<properties title="HDInsight Release Notes" pageTitle="Заметки о выпуске HDInsight для платформы Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />


#Заметки о выпуске Microsoft HDInsight

## Заметки о выпуске 15.10.14 ##

В данном исправляющем выпуске устранена утечка памяти в контроллере Templeton, которая негативным образом отражалась на пользователях, интенсивно применявших Templeton в своей работе. В некоторых случаях активные пользователи Templeton сталкивались с ошибками "Код ошибки: 500", потому что для выполнения запроса не хватало памяти.  Для избавления от проблемы требовалась перезагрузка службы Templeton. Эта проблема теперь устранена.


## Заметки о выпуске 07.10.14 ##

* При использовании в Ambari конечной точки "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" в поле *host_name* теперь возвращается полное доменное имя (FQDN) узла вместо имени этого узла. Например, вместо возврата "**headnode0**" поле теперь возвращает полное доменное имя "**headnode0.{ClusterDNS}.azurehdinsight.net**". Это изменение было продиктовано необходимостью использовать сценарии, в которых кластеры различного типа, например кластеры HBase и Hadoop, можно было бы размещать в одной виртуальной сети (VNET). Это происходит, например, при использовании HBase в качестве серверной платформы для Hadoop.

* Нами предоставлены новые настройки памяти для развертывания по умолчанию кластера HDInsight. Прошлые стандартные настройки памяти в недостаточной мере предусматривали ориентацию на количество установленных основных процессоров. Новые настройки памяти предусматривают использование 4 ядер CPU (8 контейнеров). Кластеры HDInsight перечисляются в таблице ниже: (Значения, использовавшиеся в предыдущих версиях, указываются в скобках). 
 
<table border="1">
<tr><th>Компонент</th><th>Распределение памяти</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768 МБ (ранее 512 МБ)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144 МБ (без изменения)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144 МБ (без изменения)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768 МБ (ранее 512 МБ)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (ранее -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536 МБ (ранее 1024 МБ)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (ранее -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768 МБ (ранее 1024 МБ)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (ранее -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 МБ (ранее 200 МБ)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536 МБ (без изменения)</td></tr>

</table><br>

Дополнительные сведения о параметрах памяти для YARN и MapReduce на платформе данных Hortonworks Data Platform, используемой HDInsight, см. в разделе [Определение параметров настройки памяти HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Компанией Hortonworks также предоставляется инструмент для расчета параметров памяти, требующейся для обеспечения надлежащей работы.

Ошибка HDInsight PowerShell или пакета SDK: "*Настройки кластера не обеспечивают доступ к службам HTTP*":

* Эта широко распространенная [проблема совместимости](https://social.msdn.microsoft.com/Forums/azure/ru-ru/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) может возникать вследствие отличия версии SDK и PowerShell от версии кластера. Кластеры, созданные на основе версии 8/15 и более поздней версии, поддерживают новые возможности распределения в виртуальных сетях. Но эти же возможности неправильно интерпретируются более старыми версиями SDK и PowerShell. В результате мы имеем ошибку при отправке задания. При использовании API из SDK или командлетов PowerShell для отправки заданий (**Use-AzureHDInsightCluster**, **Invoke-Hive**) выполнение этих операций может быть прервано с сообщением об ошибке "*Настройки кластера <имя_кластера> не обеспечивают доступ к службам HTTP*" или с другими сообщениями об ошибках в зависимости от выполняемой операции, например, такими как "*Невозможно подключиться к кластеру*".

* Данные проблемы совместимости устранены в последних версиях HDInsight SDK и Azure PowerShell. Мы советуем обновить HDInsight SDK до версии 1.3.1.6 или до более поздней версии, а набор инструментов Azure PowerShell до версии 0.8.8 или до более поздней версии. Получить доступ к самой новой версии HDInsight SDK можно через [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) и Инструменты Azure PowerShell, как это описано в разделе [Установка и настройка Azure PowerShell](http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/).

* SDK и PowerShell смогут работать с новыми обновлениями для кластера до тех пор, пока не изменится версия кластера. Например, версия кластера 3.1 будет всегда совместимой с текущими версиями SDK и PowerShell: 1.3.1.6 и 0.8.8 соответственно.


## Заметки о выпуске HDinsight версии 3.1 от 9.12.2014##

* Настоящий выпуск основан на платформе данных Hortonworks Data Platform (HDP) версии 2.1.5. С перечнем ошибок, исправленных в этом выпуске, можно ознакомиться на странице [Исправления этого выпуска](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) веб-сайта Hortonworks.
* В папке библиотек Pig файл "avro-mapred-1.7.4.jar" был заменен на "avro-mapred-1.7.4-hadoop2.jar". В содержании этого файла была исправлена незначительная ошибка, связанная с требованием неразрывности. Клиентам не рекомендуется выбирать зависимость, ориентируясь на название данного JAR-файла, чтобы избежать сбоев в работе после изменения названий файлов.


## Заметки о выпуске 21.08.14 ##

* Мы добавляем следующую новую конфигурацию WebHCat (HIVE-7155), которая определяет предельный объем памяти по умолчанию для задания контроллера Templeton на уровне 1 ГБ (предыдущее значение по умолчанию было 512 МБ):
	
	* templeton.mapper.memory.mb (=1024)
	* Это изменение позволяет устранить следующие ошибки, которые возникали при выполнении определенных запросов Hive вследствие более низких предельных объемов памяти: "Контейнер выходит за предельные объемы физической памяти".
	* Чтобы вернуться к старым значениям по умолчанию, можно задать значение этой конфигурации на 512 с помощью PowerShell SDK во время создания кластера, используя следующую команду:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* Имя узла роли zookeeper было изменено на zookeeper. Это оказывает влияние на разрешение имени в кластере, но не влияет на внешние REST API. При наличии компонентов с именем узла zookeepernode необходимо будет обновить их, чтобы они вместо этого использовали новые имена. Для трех узлов zookeeper используются следующие новые имена: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* Обновлена матрица поддержки версии HBase. Для производственных нагрузок HBase поддерживается только версия HDInsight 3.1 (HBase версии 0.98). Версия 3.0, которая была доступна для предварительной версии, в дальнейшем поддерживаться не будет. Во время переходного периода клиенты по-прежнему могут создавать кластеры версии 3.0. 

## Заметки о кластерах, созданных до 15.08.2014 ##

Ошибка PowerShell и SDK в HDInsight с сообщением "Настройки кластера <имя_кластера> не обеспечивают доступ к службам HTTP" (или с другими сообщениями об ошибках в зависимости от выполняемой операции, например, такими как: "Невозможно соединиться с кластером") чаще всего встречается при различии версии SDK или PowerShell и версии кластера. Кластеры, созданные на основе версии 8/15 и более поздней версии, поддерживают новые возможности распределения в виртуальных сетях. Данная возможность неправильно интерпретируется на SDK и PowerShell старых версий, что приводит к ошибкам при отправке задания. При использовании API из SDK или командлетов PowerShell для отправки таких заданий, как "Use-AzureHDInsightCluster" или "Invoke-AzureHDInsightHiveJob",  выполнение этих операций может быть прервано с отмеченными выше сообщениями об ошибках.

Данные проблемы совместимости устранены в последних версиях SDK и Azure PowerShell. Мы советуем обновить HDInsight SDK до версии 1.3.1.6 или до более поздней версии, а набор инструментов Azure PowerShell до версии 0.8.8 или более поздней версии. Получить доступ к самой новой версии HDInsight SDK можно через [NuGet](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) и Инструменты Azure PowerShell с помощью [Установщика веб-платформы Майкрософт](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

SDK и PowerShell смогут работать с новыми обновлениями для кластера до тех пор, пока не изменится версия кластера. Например, версия кластера 3.1 будет всегда совместимой с текущими версиями SDK и PowerShell: 1.3.1.6 и 0.8.8 соответственно.


## Заметки о выпуске 28.07.14 ##

* **Служба HDInsight доступна в новых регионах**: В этом выпуске присутствие службы HDInsight расширено на три новых географических региона. Клиенты HDInsight могут теперь создавать кластеры в этих регионах. 
	* Восточная Азия, 
	* Северо-центральный регион США и 
	* Южно-центральный регион США. 
* С выходом этого выпуска HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) и HDInsight v2.1 (HDP1.3, Hadoop 1.2) удаляются с портала управления Azure. Можно продолжить создавать кластеры Hadoop для этих версий с помощью командлетов HDInsight PowerShell ([New-AzureHDInsightCluster](http://msdn.microsoft.com/ru-ru/library/dn593744.aspx)) или с помощью [HDInsight SDK](http://msdn.microsoft.com/ru-ru/library/azure/dn469975.aspx). Для получения дополнительной информации см. раздел [Версии компонентов HDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/).
* Изменения платформы данных Hortonworks Data Platform (HDP) в этом выпуске: 

<table border="1">
<tr><th>Изменения</th><th>HDP</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Без изменений</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Без изменений</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Заметки о выпуске 24/06/2014 ##

В этом выпуске содержится несколько новых улучшений службы HDInsight: 

* **Доступность HDP 2.1**: Служба HDInsight 3.1, содержащая HDP 2.1, теперь общедоступна и представляет собой версию по умолчанию для новых кластеров.
* **HBase - улучшения портала управления Azure**: Кластеры HBase становятся доступны в предварительной версии. Теперь можно создавать кластеры HBase из портала с помощью всего 3 щелчков.

![](http://i.imgur.com/cmOl5fM.png)

Благодаря кластерам HBase можно создавать различные рабочие нагрузки на HDInsight в реальном времени, начиная от интерактивных веб-сайтов с большими наборами данных до служб, сохраняющих данные датчиков и телеметрии от миллионов конечных точек. На следующем шаге было бы необходимо проанализировать данные в этих рабочих нагрузках с помощью заданий Hadoop. Это можно сделать прямо в службе HDInsight через такие представленные интерфейсы, как PowerShell и панель мониторинга кластеров Hive.

### Библиотеке Apache Mahout теперь предустановлена в службе HDInsight 3.1 ###

 Библиотека [Mahout](http://hortonworks.com/hadoop/mahout/) предустановлена в кластерах HDInsight 3.1 Hadoop. Благодаря этому можно выполнять задания Mahout без необходимости дополнительно настраивать кластер. Например, можно выйти на удаленный кластер Hadoop с использованием протокола удаленного рабочего стола (RDP) и без дополнительных шагов выполнить команду Mahout "Привет всем!":

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Более подробную информацию об этой процедуре см. в документации [Пример Breiman](https://mahout.apache.org/users/classification/breiman-example.html) на веб-сайте Apache Mahout. 


### Запросы Hive могут использовать Tez в HDinsight 3.1 ###

Hive 0.13 теперь доступен в HDInsight 3.1 и может выполнять запросы с использованием Tez, что даст возможность значительно улучшить производительность. 
Среда Tez теперь доступна по умолчанию для запросов Hive. Чтобы ее использовать, необходимо ее выбрать. Можно включить Tez, выполнив следующий фрагмент кода:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

На платформе Hortonworks опубликована подробная разбивка улучшений производительности запросов Hive с использованием Tez по данным стандартных тестов производительности. Подробную информацию см. в разделе [Тест производительности Apache Hive 13 для Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

Более подробную информацию об использовании Hive с Tez см. в разделе [Hive на вики-странице Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Глобальная доступность
С выходом Azure HDInsight на Hadoop 2.2 корпорация Microsoft сделала службу HDInsight доступной во все основных регионах расположения Azure. В частности, подключены центры обработки данных в Западной Европе и Юго-Восточной Азии. Благодаря этому клиенты могут подобрать кластеры в центре обработки данных, который расположен неподалеку и, возможно, находится в зоне со схожими требованиями по соблюдению законодательства. 


###Полезные советы по работе с кластерами разных версий

**Хранилище метаданных Oozie, используемое с кластером HDInsight 3.1, обратно не совместимо с кластерами HDInsight 2.1, и его использование с кластерами предыдущих версий невозможно.**

Пользовательская база данных хранилища метаданных Oozie, развернутая с помощью кластера HDInsight 3.1, не может использоваться с кластером HDInsight 2.1. В данном случае это невозможно, даже если хранилище метаданных было развернуто с кластером версии 2.1. Такой сценарий не поддерживается, поскольку при использовании с кластером версии 3.1 схема хранилища метаданных обновляется и становится несовместимой для работы с кластерами версии 2.1. При применении хранилища метаданных Oozie, использовавшегося с кластером HDInsight 3.1, кластер версии 2.1 окажется не работоспособным. 

**Хранилища метаданных Oozie не могут принадлежать разным кластерам**
В более общем и непосредственном смысле можно утверждать, что Хранилища метаданных Oozie привязаны к конкретным кластерам, и совместное их использование разными кластерами невозможно.

###Критические изменения

**Синтаксис префикса**:
В кластерах HDInsight 3.0  и 3.1 поддерживается только синтаксис "wasb://". Прежний синтаксис "asv: / /" поддерживается в кластерах HDInsight 2.1 и 1.6, однако не поддерживается в кластерах HDInsight 3.0 или в последующих версиях. Это означает, что все задания, отправляемые в кластер HDInsight версии 3.0  или 3.1, в которых явно используется синтаксис "asv://", завершатся неудачей. Вместо этого следует использовать синтаксис wasb://. Кроме того, сбоем будут завершатся задания, отправляемые в кластеры HDInsight 3.0 ил 3.1, которые созданы с существующим метахранилищем, содержащим явные ссылки на ресурсы с использованием синтаксиса "asv://". Эти метахранилища потребуется создать повторно с использованием синтаксиса wasb://, чтобы адресовать ресурсы. 


**Порты**: Изменены порты, используемые службой HDInsight. Номера ранее используемых портов находились во временном диапазоне портов ОС Windows. Порты распределяются автоматически из предопределенного временного диапазона для краткосрочных обменов данными на базе протокола IP. Новый набор допустимых номеров портов службы платформы данных Hortonworks (HDP) теперь находится вне этого диапазона, что позволяет избежать конфликтов, которые могли бы возникать с портами, уже используемыми службами, выполняемыми на головном узле. Новые номера портов не должны вызывать никаких критических изменений. В настоящее время используются следующие номера:

 **HDInsight 1,6 (HDP 1,1)**
<table border="1">
<tr><th>Имя</th><th>Значение</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 и 3.1 (HDP 2.0 и 2.1)**
<table border="1">
<tr><th>Имя</th><th>Значение</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Зависимости 

В HDInsight 3.x (HDP2.x) добавлены следующие зависимости:

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

В HDInsight 3.x (HDP2.x) больше не существуют следующие зависимости:

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Изменения версий 

Между HDInsight 2.x (HDP1.x) и HDInsight 3.x (HDP2.x) были сделаны следующие изменения версий:

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Драйверы
 Драйвер JDBC для SQL Server используется в HDInsight и не используется для внешних операций. Если нужно подключиться к HDInsight с использованием ODBC, следует использовать драйвер ODBC для Microsoft Hive. Дополнительные сведения об использовании Hive ODBC см. в документе [Подключение Excel к HDInsight с помощью драйвера ODBC для Microsoft Hive][connect-excel-with-hive-ODBC].


### Исправления ошибок ###

В этом выпуске были обновлены следующие версии HDInsight  (платформа данных Hortonworks - HDP) несколькими исправлениями ошибок:

* HDInsight 2,1 (HDP 1,3)
* HDInsight 3,0 (HDP 2,0)
* HDInsight 3.1 (HDP 2.1)

## Заметки о выпуске Hortonworks ##

Заметки о выпуске HDP, используемые версиями кластерами HDInsight, доступны в следующих местоположениях.

* Кластер HDInsight версии 3.1 использует пакет Hadoop, основанный на [платформе данных Нortonworks Data Platform 2.1][hdp-2-1-1].(Этот кластер Hadoop создается по умолчанию при использовании портала Azure HDInsight.)

* Кластер HDInsight версии 3.0 использует пакет Hadoop, основанный на [платформе данных Hortonworks Data Platform 2.0][hdp-2-0-8].

* Кластер HDInsight версии 2.1 использует пакет Hadoop, основанный на [платформе данных Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* Кластер HDInsight версии 1.6 использует пакет Hadoop, основанный на [платформе данных Hortonworks Data Platform 1.1][hdp-1-1-0]. 




[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html




