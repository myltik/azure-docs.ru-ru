<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Заметки о выпуске Microsoft HDInsight

## Заметки о выпуске 21/08/2014

-   Мы добавляем следующую новую конфигурацию WebHCat (HIVE-7155), которая определяет предельный объем памяти по умолчанию для задания контроллера Templeton на уровне 1 ГБ (предыдущее значение по умолчанию было 512 МБ):

    -   templeton.mapper.memory.mb (=1024)
    -   Это изменение позволяет устранить следующие ошибки, которые возникали при выполнении определенных запросов Hive вследствие более низких предельных объемов памяти: «Контейнер выходит за предельные объемы физической памяти».
    -   Чтобы вернуться к старым значениям по умолчанию, можно задать значение этой конфигурации на 512 с помощью PowerShell SDK во время создания кластера, используя следующую команду:

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   Имя узла роли zookeeper было изменено на zookeeper. Это оказывает влияние на разрешение имени в кластере, но не влияет на внешние REST API. При наличии компонентов с именем узла zookeepernode необходимо будет обновить их, чтобы они вместо этого использовали новые имена. Для трех узлов zookeeper используются следующие новые имена:

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   Обновлена матрица поддержки версии HBase. Для производственных нагрузок HBase поддерживается только версия HDInsight 3.1 (HBase версии 0.98). Версия 3.0, которая была доступна для предварительной версии, в дальнейшем поддерживаться не будет. Во время переходного периода клиенты по-прежнему могут создавать кластеры версии 3.0.

## Заметки о выпуске 28/07/2014

-   **Служба HDInsight доступна в новых регионах**: В этом выпуске присутствие службы HDInsight расширено на три новых географических региона. Клиенты HDInsight могут теперь создавать кластеры в этих регионах.

    -   Восточная Азия,
    -   Северо-центральный регион США и
    -   Южно-центральный регион США.
-   С выходом этого выпуска HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) и HDInsight v2.1 (HDP1.3, Hadoop 1.2) удаляются с портала управления Azure. Можно продолжить создавать кластеры Hadoop для этих версий с помощью командлетов HDInsight PowerShell ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) или с помощью [HDInsight SDK][HDInsight SDK]. Для получения дополнительной информации см. раздел [Версии компонентов HDInsight][Версии компонентов HDInsight].
-   Изменения платформы данных Hortonworks Data Platform (HDP) в этом выпуске:

| HDP               | изменения                                                    |
|-------------------|--------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | Без изменений                                                |
| HDP 2.0 / HDI 3.0 | Без изменений                                                |
| HDP 2.1 / HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## Заметки о выпуске 24/06/2014

В этом выпуске содержится несколько новых улучшений службы HDInsight:

-   **Доступность HDP 2.1**: Служба HDInsight 3.1, содержащая HDP 2.1, теперь общедоступна и представляет собой версию по умолчанию для новых кластеров.
-   **HBase – улучшения портала управления Azure**: Кластеры HBase становятся доступны в предварительной версии. Теперь можно создавать кластеры HBase из портала с помощью всего 3 щелчков.

![][0]

Благодаря кластерам HBase можно создавать различные рабочие нагрузки на HDInsight в реальном времени, начиная от интерактивных веб-сайтов с большими наборами данных до служб, сохраняющих данные датчиков и телеметрии от миллионов конечных точек. На следующем шаге было бы необходимо проанализировать данные в этих рабочих нагрузках с помощью заданий Hadoop. Это можно сделать прямо в службе HDInsight через такие представленные интерфейсы, как PowerShell и панель мониторинга кластеров Hive.

### Библиотеке Apache Mahout теперь предустановлена в службе HDInsight 3.1

Библиотека [Mahout][Mahout] предустановлена в кластерах HDInsight 3.1 Hadoop. Благодаря этому можно выполнять задания Mahout без необходимости дополнительно настраивать кластер. Например, можно выйти на удаленный кластер Hadoop с использованием протокола удаленного рабочего стола (RDP) и без дополнительных шагов выполнить команду Mahout «Привет всем!»:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Более подробную информацию об этой процедуре см. в документации [Пример Breiman][Пример Breiman] на веб-сайте Apache Mahout.

### Запросы Hive могут использовать Tez в HDinsight 3.1

Hive 0.13 теперь доступен в HDInsight 3.1 и может выполнять запросы с использованием Tez, что даст возможность значительно улучшить производительность.
Среда Tez теперь доступна по умолчанию для запросов Hive. Чтобы ее использовать, необходимо ее выбрать. Можно включить Tez, выполнив следующий фрагмент кода:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

На платформе Hortonworks опубликована подробная разбивка улучшений производительности запросов Hive с использованием Tez по данным стандартных тестов производительности. Подробную информацию см. в разделе [Тест производительности Apache Hive 13 для Enterprise Hadoop][Тест производительности Apache Hive 13 для Enterprise Hadoop].

Более подробную информацию об использовании Hive с Tez см. в разделе [Hive на вики-странице Tez][Hive на вики-странице Tez].

### Глобальная доступность

С выходом Azure HDInsight на Hadoop 2.2 корпорация Microsoft сделала службу HDInsight доступной во все основных регионах расположения Azure. В частности, подключены центры обработки данных в Западной Европе и Юго-Восточной Азии. Благодаря этому клиенты могут подобрать кластеры в центре обработки данных, который расположен неподалеку и, возможно, находится в зоне со схожими требованиями по соблюдению законодательства.

### Критические изменения

**Синтаксис префикса**:
В кластерах HDInsight 3.0 и 3.1 поддерживается только синтаксис "wasb: / /". Прежний синтаксис "asv: / /" поддерживается в кластерах HDInsight 2.1 и 1.6, однако не поддерживается в кластерах HDInsight 3.0 или в последующих версиях. Это означает, что все задания, отправляемые в кластер HDInsight версии 3.0 или 3.1, в которых явно используется синтаксис "asv://", завершатся неудачей. Вместо этого следует использовать синтаксис "wasb://". Кроме того, сбоем будут завершатся задания, отправляемые в кластеры HDInsight 3.0 ил 3.1, которые созданы с существующим метахранилищем, содержащим явные ссылки на ресурсы с использованием синтаксиса "asv://". Эти метахранилища потребуется создать повторно с использованием синтаксиса wasb://, чтобы адресовать ресурсы.

**Порты**: Изменены порты, используемые службой HDInsight. Номера ранее используемых портов находились во временном диапазоне портов ОС Windows. Порты распределяются автоматически из предопределенного временного диапазона для краткосрочных обменов данными на базе протокола IP. Новый набор допустимых номеров портов службы платформы данных Hortonworks (HDP) теперь находится вне этого диапазона, что позволяет избежать конфликтов, которые могли бы возникать с портами, уже используемыми службами, выполняемыми на головном узле. Новые номера портов не должны вызывать никаких критических изменений. В настоящее время используются следующие номера:

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
Имя

</th>
<th>
Значение

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 и 3.1 (HDP 2.0 и 2.1)**

<table border="1">

<tr>
<th>
Имя

</th>
<th>
Значение

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### Зависимости

В HDInsight 3.x (HDP2.x) добавлены следующие зависимости:

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

В HDInsight 3.x (HDP2.x) больше не существуют следующие зависимости:

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### Изменения версий

Между HDInsight 2.x (HDP1.x) и HDInsight 3.x (HDP2.x) были сделаны следующие изменения версий:

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.ВЫПУСК'] -\> ['0.8.0.ВЫПУСК']

### Драйверы

Драйвер JDBC для SQL Server используется в HDInsight и не используется для внешних операций. Если нужно подключиться к HDInsight с использованием ODBC, следует использовать драйвер ODBC для Microsoft Hive. Дополнительные сведения об использовании Hive ODBC см. в документе[Подключение Excel к HDInsight с помощью драйвера ODBC для Microsoft Hive] [connect-excel-with-hive-ODBC].

### Исправления ошибок

В этом выпуске были обновлены следующие версии HDInsight (платформа данных Hortonworks - HDP) несколькими исправлениями ошибок:

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Заметки о выпуске Hortonworks

Заметки о выпуске HDP, используемые версиями кластерами HDInsight, доступны в следующих местоположениях.

-   Кластер HDInsight версии 3.1 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 2.1][Платформе данных Нortonworks Data Platform 2.1]. (Этот кластер Hadoop создается по умолчанию при использовании портала Azure HDInsight.)

-   Кластер HDInsight версии 3,0 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 2,0][Платформе данных Нortonworks Data Platform 2,0].

-   Кластер HDInsight версии 2,1 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 1,3][Платформе данных Нortonworks Data Platform 1,3].

-   Кластер HDInsight версии 1.6 использует пакет Hadoop, основанный на [Платформе данных Нortonworks Data Platform 1.1][Платформе данных Нortonworks Data Platform 1.1].

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/ru-ru/library/dn593744.aspx
  [HDInsight SDK]: http://msdn.microsoft.com/ru-ru/library/azure/dn469975.aspx
  [Версии компонентов HDInsight]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/
  [0]: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Пример Breiman]: https://mahout.apache.org/users/classification/breiman-example.html
  [Тест производительности Apache Hive 13 для Enterprise Hadoop]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [Hive на вики-странице Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Платформе данных Нortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Платформе данных Нortonworks Data Platform 2,0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Платформе данных Нortonworks Data Platform 1,3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Платформе данных Нortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
