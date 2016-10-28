<properties
	pageTitle="Что представляют собой различные компоненты, доступные в кластере HDInsight | Microsoft Azure"
	description="HDInsight поддерживает несколько компонентов и версий кластера Hadoop, которые могут быть развернуты в любое время. См. поддерживаемые версии платформы данных HortonWorks Data Platform (HDP) и Hadoop."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="jgao"/>


# Что представляют собой различные компоненты Hadoop, доступные в HDInsight?

Узнайте о разных уровнях обслуживания, предлагаемых HDInsight, а также о версиях различных компонентов Hadoop, входящих в HDInsight.

## HDInsight "Стандартный" и HDInsight "Премиум"

Azure HDInsight предлагает облачные решения для работы с большими данными в двух категориях: **Стандартный** и **Премиум**. В таблице внизу раздела перечислены компоненты, доступные **только** в рамках категории "Премиум". Компоненты, не указанные в этой таблице явно, доступны в рамках категории "Стандартный".

>[AZURE.NOTE] Предложение HDInsight "Премиум" сейчас находится в состоянии предварительной версии и доступно только для кластеров Linux.

| Компонент HDInsight "Премиум" | Описание |
|--------------|---------------|
| Microsoft R Server (предварительная версия) | Microsoft R Server — это наиболее широко развернутая платформа аналитики корпоративного класса для масштабируемого R. Язык R поддерживает разнообразную статистику для больших данных, прогнозирующее моделирование и возможности машинного обучения. Теперь в рамках HDInsight "Премиум" вы можете создавать кластер HDInsight с R Server, готовый для использования с большими наборами данных и моделями. Эта новая возможность предоставляет статистикам и специалистам по обработке данных знакомый интерфейс R, который можно масштабировать по запросу через HDInsight, без затрат на настройку и обслуживание кластера. <br> <br>Дополнительные сведения см. в статье [Начало работы с R Server в HDInsight](hdinsight-hadoop-r-server-get-started.md).

### Типы кластеров, поддерживаемые для предложения "Премиум"

В следующей таблице перечислены типы кластеров HDInsight и приведена матрица поддержки HDInsight "Премиум".

| Тип кластера | Стандартная | Premium |
|--------------|---------------|--------------|
| Hadoop | Да | Да |
| Spark | Да | Да |
| HBase | Да | Нет |
| Storm | Да | Нет |

Эта таблица будет обновляться по мере включения дополнительных типов кластеров в HDInsight "Премиум".

### Цены и соглашение об уровне обслуживания

Сведения о ценах и соглашение об уровне обслуживания для HDInsight "Премиум" см. в статье [Цены на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Компоненты Hadoop, доступные в разных версиях HDInsight

Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий создает конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии. Версии компонентов, связанные с версиями кластера HDInsight, приведены в следующей таблице. Обратите внимание, что в настоящее время Azure HDInsight по умолчанию использует версию кластера 3.4 на основе HDP 2.4 (с 14.09.2016).

> [AZURE.NOTE] Версия по умолчанию из службы может быть изменена без уведомления. Рекомендуется указать версию при создании кластеров с использованием пакета SDK для .NET, Azure PowerShell и интерфейса командной строки Azure, если используется зависимость версии.

Компонент|HDInsight версии 3.4 (по умолчанию) | HDInsight версия 3.3 | HDInsight версия 3.2 |HDInsight версия 3.1 |HDInsight версия 3,0|
---|---|---|---|---|---
Hortonworks Data Platform|2\.4|2\.3|2\.2|2\.1.7|2,0|
Apache Hadoop & YARN|2\.7.1|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive & HCatalog|1\.2.1|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.2|1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.6.0 (только для Linux)|1\.5.2 (только для Linux, экспериментальная сборка)|1\.3.1 (только для Windows)|||


**Получение сведений о текущей версии компонентов**

Версии компонентов, связанных с версиями кластера HDInsight, могут быть изменены в будущих обновлениях для HDInsight. Один из способов определения доступных компонентов и проверки версий,которые должны использоваться для кластера, заключается в применении интерфейса API REST Ambari. Для извлечения сведений о компоненте службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Ambari][ambari-docs]. Еще один способ получения этих сведений — вход в кластер с помощью удаленного рабочего стола и непосредственное изучение содержимого каталога C:\\apps\\dist.


**Заметки о выпуске**

Дополнительные заметки о выпусках последних версий HDInsight см. в разделе [Заметки о выпуске НDInsight](hdinsight-release-notes.md).


## Поддерживаемые версии HDInsight
В следующей таблице перечислены доступные в настоящее время версии HDInsight, соответствующие версии платформы Hortonworks Data Platform (HDP), которые они используют, и даты их выхода. Также указываются даты прекращения поддержки и даты устаревания, если они известны. Обратите внимание на следующее:

* Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для HDInsight 2.1 и последующих версий. Они недоступны для кластеров HDInsight 1.6.
* При истечении срока поддержки для определенной версии она может стать недоступной через портал Azure. В таблице ниже указаны версии, доступные на классическом портале Azure. Доступ к версиям кластера по-прежнему можно будет получить с помощью параметра `Version` в командлете Windows PowerShell [New-AzureHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) и пакета SDK .NET до даты его устаревания.

Версия HDInsight|Версия HDP|ОС виртуальной машины|Высокая доступность|Дата выпуска|Доступно на портале Azure|Дата прекращения поддержки|Дата прекращения сопровождения
---|---|---|---|---|---|---|---
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|Да|03/29/2016|Да||
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 LTS или Windows Server 2012R2|Да|12\.02.2015|Да|27\.06.2016|31\.07.2017
HDI 3.2|HDP 2.2|Ubuntu 12.04 LTS или Windows Server 2012R2|Да|18\.02.2015|Да|01\.03.2016|01\.04.2017
HDI 3,1|HDP 2,1|Windows Server 2012R2|Да|24\.06.2014|Нет|18\.05.2015|30\.06.2016
HDI 3,0|HDP 2,0|Windows Server 2012R2|Да|11\.02.2014|Нет|17\.09.2014|30\.06.2015
HDI 2,1|HDP 1,3|Windows Server 2012R2|Да|28\.10.2013|Нет|12\.05.2014|31\.05.2015
HDI 1.6|HDP 1.1||Нет|28\.10.2013|Нет|26\.04.2014|31\.05.2015

**Развертывание кластеров не по умолчанию**

### Соглашение об уровне обслуживания (SLA) для версий кластера HDInsight

Соглашение об уровне обслуживания определяется в терминах "Окна поддержки". Под окном поддержки понимается период времени, в течение которого определенная версия кластера HDInsight поддерживается службой поддержки клиентов корпорации Майкрософт. Кластер HDInsight находится вне окна поддержки, если **дата истечения срока технической поддержки его версии** уже миновала. Список поддерживаемых версий кластера HDInsight можно найти в приведенной выше таблице. Дата истечения срока действия технической поддержки для конкретной версии HDInsight (обозначается как версия X) рассчитывается как наиболее поздняя из следующих дат (в случае доступности более новой версии Х+1):

- Формула 1: добавьте 180 дней к дате выхода версии X кластера HDInsight.
- Формула 2: добавьте 90 дней к дате, когда версия X + 1 (следующая за версией X) кластера HDInsight стала доступной на портале.

**Дата устаревания** — это дата, после которой версия кластера не может быть создана в HDInsight.

> [AZURE.NOTE] Кластеры HDInsight на основе Windows (включая версии 2.1, 3.0, 3.1, 3.2 и 3.3) работают на под управлением семейства версий 4 гостевой ОС Azure, которая использует 64-разрядную версию Windows Server 2012 R2 и поддерживает .NET Framework 4.0, 4.5, 4.5.1. и 4.5.2.

## Заметки о выпуске Hortonworks, связанные с версиями HDInsight##

* Кластер HDInsight версии 3.4 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). Это кластер Hadoop **по умолчанию**, который создается при использовании портала.



* Кластер HDInsight версии 3.3 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
	* Заметки о выпуске Apache Storm доступны [здесь](https://storm.apache.org/2015/11/05/storm0100-released.html).
	* Заметки о выпуске Apache Hive доступны [здесь](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* Кластер HDInsight версии 3.2 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.2][hdp-2-2].

	* Заметки о выпуске для отдельных компонентов Apache — [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* Кластер HDInsight версии 3.1 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.1.7][hdp-2-1-7]. Кластеры HDInsight 3.1, созданные до 07.11.2014, основаны на платформе [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* Кластер HDInsight версии 3.0 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.0][hdp-2-0-8].

* Кластер HDInsight версии 2.1 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 1.3][hdp-1-3-0].

* Кластер HDInsight версии 1.6 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0921_2016-->